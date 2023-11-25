# System

Example basic System that figures out how many active vehicles are spawned in the game:

```csharp
public class VehicleCounterSystem : GameSystemBase {
    private EntityQuery m_VehicleQuery;
    public int current_vehicle_count = 0;

    protected override void OnCreate() {
        base.OnCreate();
        this.m_VehicleQuery = this.GetEntityQuery(new EntityQueryDesc() {
            All = new ComponentType[1] {
                ComponentType.ReadOnly<Vehicle>()
            },
            None = new ComponentType[2] {
                ComponentType.ReadOnly<Deleted>(),
                ComponentType.ReadOnly<Temp>()
            }
        });

        var count = this.m_VehicleQuery.CalculateEntityCount();
        UnityEngine.Debug.Log($"OnCreate - VehicleCounter - Vehicle Count: {count}");
        this.PrintPeriodically();
    }

    protected override void OnUpdate() {
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
        updateSystem.UpdateAt<VehicleCounterSystem>(SystemUpdatePhase.PostSimulation);
    }
}
```