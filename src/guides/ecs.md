# ECS

> Introduction to ECS, and how Cities: Skylines 2 uses ECS as a corner-stone of their architecture

First, a short list of definitions:

- ECS - Entity Component System - Software architecture for decoupled design, better memory access patterns and making parallelism easy
- Entity - An ID representing *something* in a game, like the player's in game character
- Component - A carrier of data, attached to the Entity. Like the character's health, or sprite position.
- System - Queries for Components, and reads/mutates them. Behaviour like "When poisoned, drain health".

ECS not a new architecture, but have become more prominent as of late as it scales very well when you have large amount data and whoever is running the game, have multiple cores in the their CPU.

It gives you a decoupled design as you're acting on components rather than a composition of components, so you can for example easily separate code related to the position of a Entity, and the code related to how much health the Entity has.

## Entity Component System - Basic Example

A quick demonstration of how ECS could be done (pseudo-code):

We first define three Components:

```javascript
class Position extends Component {
    int x = 0
    int y = 0
}

class Health extends Component {
    int current_health = 100
}

class Poisoned extends Component {}
```

And then our Systems that act on those Components:

```javascript
function UpdatePosition(query: List<Position>) {
    for position in query {
        position.x = position.x + 0.1
    }
}

function DrawCharacter(query: List<Position>) {
    for position in query {
        // Code for drawing a character of some kind, at the position
    }
}

function UpdateHealth(query: List<(Poisoned, Health)>) {
    for (poison, health) in query {
        health.current_health = health.current_health - 1
    }
}
```

First we define a System that will continiously query for Position Components, and when it finds any, update the position to go slightly to the left.

Second we define a System that draws our actual sprite to the display, to indicate where our player is. This will slowly change position as the position is updated in another system.

Last, we define a System that finds any Entities that have both a Poisoned and a Health component, and if it finds any, iterates over them to decrement the Health.

Finally we can instantiate our game:

```javascript
var app = create_game();
app.AddSystem(UpdatePosition, phase.Update)
   .AddSystem(DrawCharacter,  phase.Update)
   .AddSystem(UpdateHealth,   phase.Update)
```

We not only create the game, but declare in what phases systems will run in. Currently, we only have one phase, but larger games will have multiple. If you want, you can specify Systems to run before/after other specific Systems, in case they have to run in a particular order.

With this architecture, we can simply add/remove the `Poisoned` component from a Entity at runtime, to let the system for removing health to run. Typically, ECS allows you to Querying for Components like our example above, so while the system is run continously, the query won't always result in any hits, and then the health won't be affected. But once a Entity has the Poisoned and Health component on it, then the System will continue to drain health.

Creating a new thing in the game world typically looks something like this:

```javascript

var player_character = app.new_entity()
    .with_component(Position)
    .with_component(Health)
```

Then at some later stage, components can be added and removed:

```javascript
function OnHit(query: List<PhysicsHit>) {
    for hit in query {
        hit.entity.add_component(Poisoned)
    }
}
```

## ECS In Cities: Skylines 2

ECS in Cities: Skylines 2 begin at the place where all systems are put in one big list, with specific declaration at what "Phase" it'll be executed at.

Basically everything about how the game behaves or data it reads/mutates, is done via Systems and Components, for example: `LoadGameSystem`, `AutoSaveSystem`, `BoardingVehicleSystem`, `BulldozeToolSystem` and `TransportInfoviewUISystem` are implemented as any other ECS System in the game, and put in the giant list of what executes before/after what.

Each system is defined as to where in the list it should be executed, and at what "Phase" as well.

The Phases are defined as a public enum, `Game.SystemUpdatePhase`

Example:

```csharp
...
Rendering = 14
PreTool = 15
PostTool = 16
ToolUpdate = 17
ClearTool = 18
...
```

The most basic API method  is `UpdateAt` which defines at what Phase a System should be run at:

```
updateSystem.UpdateAt<ApplyObjectsSystem>(SystemUpdatePhase.ApplyTool);
```

The example above would run the `ApplyObjectsSystem` System when the active Phase is `ApplyTool`.

Sometimes you want to run a particular System before/after another, then `UpdateBefore` and `UpdateAfter` comes in handy. Example:

```csharp
updateSystem.UpdateAt<ResourceAvailabilitySystem>(SystemUpdatePhase.GameSimulation);
updateSystem.UpdateAfter<EarlyGameOutsideConnectionTriggerSystem, ResourceAvailabilitySystem>(SystemUpdatePhase.GameSimulation);
```

This would make the `EarlyGameOutsideConnectionTriggerSystem` System run after the `ResourceAvailabilitySystem` system, while in the `GameSimulation` Phase.

Going further in, let's look at a specific System in CS2, the `BrandPopularitySystem` System in `Game.City`.

It sets up Queries for Components like we did in our pseudo-ECS game above:

```
this.m_ModifiedQuery = this.GetEntityQuery(new EntityQueryDesc() {
  All = new ComponentType[1] { ComponentType.ReadOnly<CompanyData>() },
  Any = new ComponentType[2] { ComponentType.ReadOnly<Created>(), ComponentType.ReadOnly<Deleted>() },
  None = new ComponentType[1] { ComponentType.ReadOnly<Temp>() }
});
```

This Query tries to find something that matches the following conditions:

- Has all of the Components: CompanyData
- Has any of the Components: Created or Deleted
- Doesn't have any of the Components: Temp
