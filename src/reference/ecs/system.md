# System

Example basic System that figures out how many active vehicles are spawned in the game:

```csharp
public class VehicleCounterSystem : GameSystemBase {
    // Define a EntityQuery we can create in OnCreate and use in OnUpdate
    private EntityQuery m_VehicleQuery;
    // Field we can use to keep track of how many vehicles we've found from our query.
    public int current_vehicle_count = 0;

    protected override void OnCreate() {
        base.OnCreate();
        // Since we extend from GameSystemBase we have access to GetEntityQuery in order
        // to query the game world for entities with different components
        this.m_VehicleQuery = this.GetEntityQuery(new EntityQueryDesc() {
            // `All` selects all entities that have all of the components mentioned, in
            // this case `Vehicle`. We also mark it as `ReadOnly` as we don't need to
            // mutate the data, others can access it at the same time
            All = new ComponentType[1] {
                ComponentType.ReadOnly<Vehicle>()
            },
            // `None` deselects the entities that has any of the mentioned components
            None = new ComponentType[2] {
                ComponentType.ReadOnly<Deleted>(),
                ComponentType.ReadOnly<Temp>()
            }
        });
        // We do a initial count of how many entities we found that matches the query
        // we wrote above
        var count = this.m_VehicleQuery.CalculateEntityCount();

        // For debugging purposes, we output the current count to the console
        UnityEngine.Debug.Log($"OnCreate - VehicleCounter - Vehicle Count: {count}");
        this.PrintPeriodically();
    }

    protected override void OnUpdate() {
        // Here we re-calculate the current vehicles count based on how many entities
        // the query now returns.
        var count = this.m_VehicleQuery.CalculateEntityCount();
        this.current_vehicle_count = count;
    }

    private async void PrintPeriodically() {
        while (true) {
            UnityEngine.Debug.Log($"OnUpdate - VehicleCounter - Vehicle Count: {this.current_vehicle_count}");
            await Task.Delay(1000 * 10);
        }
    }
}
```

Inject your System with Harmony to run at the `PostSimulation` Phase, to make it enabled in the game:

```csharp
[HarmonyPatch(typeof(SystemOrder))]
public static class SystemOrderPatch {
    [HarmonyPatch("Initialize")]
    [HarmonyPostfix]
    public static void Postfix(UpdateSystem updateSystem) {
        // Add our defined VehicleCounterSystem to the update system which makes it part of
        // the game loop. Make sure it runs at the Phase `PostSimulation`
        updateSystem.UpdateAt<VehicleCounterSystem>(SystemUpdatePhase.PostSimulation);
    }
}
```
