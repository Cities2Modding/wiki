# Game UI


## UI Bindings

Symbols used in the API

- AddBinding: A method to add a new binding to the UI system. It links a specific UI element or event to a corresponding action or state in the game.
- TriggerBinding: A binding type that associates a UI trigger (like a button click or a toggle) with a specific method or action.
- InputManager.instance.FindAction: Used to find and set up proxy actions for gathering user inputs, like camera controls or menu navigation.
- AddUpdateBinding: A method to add a binding that updates the UI based on changes in the game's state.
- GetterValueBinding: A type of binding that retrieves a value from the game state and updates the UI accordingly.

## Event Triggers and Handlers

Event triggers and handlers respond to user interactions with the UI, executing specific actions in response.

### Code Example
```csharp
// Adding an event trigger for a button click
this.AddBinding(new TriggerBinding("Menu", "PlayButton", 
    () => StartGame()));
```

## Proxy Actions

Proxy Actions translate user inputs into game actions, allowing for flexible input configurations.

### Code Example
```csharp
// Setting up a proxy action for player movement
this.m_MoveAction = InputManager.instance.FindAction("Player", "Move");
```

## Dynamic UI Updates

Dynamic UI updates ensure that UI elements reflect real-time changes in the game's state.

### Code Example
```csharp
// Updating a health bar dynamically
this.AddUpdateBinding(new GetterValueBinding<float>("Player", "Health", 
    () => player.health));
```

## GetterValueBinding

GetterValueBinding is used for displaying specific data from the game in the UI.

### Code Example
```csharp
// Displaying the player's score
this.AddBinding(new GetterValueBinding<int>("Player", "Score", 
    () => player.score));
```

## `AddUpdateBinding` vs `AddBinding`

- AddUpdateBinding
    - Purpose: It's used to create a binding that will regularly update based on changes in the game's state or logic.
    - Functionality: Suitable for dynamic UI elements that need to reflect the current state of the game, like a score display or a health bar.

- AddBinding:
    - Purpose: A general method to create a binding between a UI element and a game logic element. It can be used for both static and dynamic interactions.
    - Functionality: It's more general-purpose than AddUpdateBinding and can be used for a wide range of UI binding needs, including event triggers.

## Example UI ECS System

A UI ECS System should extend from `UISystemBase`, which helps to manage the bindings a bit more.

UI Systems should be included at the `UIUpdate` Phase.

Make sure to call `base.OnCreate();` as otherwise your [Bindings](../reference/game-ui/gettervaluebinding.md) might not work

Example:

```csharp
class MyUISystem : UISystemBase {
    private int people_i_want_to_hug = 100;
    private string kGroup = "myowncoolmod_namespace";
    protected override void OnCreate() {
        base.OnCreate();

        // Update the UI when people_i_want_to_hug changes
        this.AddUpdateBinding(new GetterValueBinding<int>(this.kGroup, "people_i_want_to_hug", () => {
            return this.people_i_want_to_hug;
        }));

        AddHumanPeriodically();
    }
    private async void AddHumanPeriodically() {
        while (true) {
            UnityEngine.Debug.Log("Adding!");
            await Task.Delay(5000);
            people_i_want_to_hug += 1;
        }
    }
}
```

Adding the system to the `updateSystem`:

```csharp
updateSystem.UpdateAt<MyOwnCoolUI>(SystemUpdatePhase.UIUpdate);
```

With BepInEx and Harmony:

```csharp
[HarmonyPatch(typeof(SystemOrder))]
public static class SystemOrderPatch {
    [HarmonyPatch("Initialize")]
    [HarmonyPostfix]
    public static void Postfix(UpdateSystem updateSystem) {
        updateSystem.UpdateAt<MyUISystem>(SystemUpdatePhase.UIUpdate);
    }
}
```
