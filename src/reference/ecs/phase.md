# Phase

> Phases help you decide when your [System](./system.md) should run, and optionally lock it to run before/after another specific [System](./system.md)

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
