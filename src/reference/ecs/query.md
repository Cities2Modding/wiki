# Query

Small example for getting all `Vehicle` [Components](./component.md) that doesn't have the `Deleted` or `Temp` [Components](./component.md) (all active vehicles) inside a `GameSystemBase` which your [System](./system.md) should extend from:

```csharp
this.m_VehicleQuery = this.GetEntityQuery(new EntityQueryDesc() {
    All = new ComponentType[1] {
        ComponentType.ReadOnly<Vehicle>()
    },
    None = new ComponentType[2] {
        ComponentType.ReadOnly<Deleted>(),
        ComponentType.ReadOnly<Temp>()
    }
});
```

Then you can do things like count the number of found [Entities](./entity.md) with that matching set:

```csharp
var count = this.m_VehicleQuery.CalculateEntityCount();
```