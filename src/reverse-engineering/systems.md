### A Typical System

```csharp
using Unity.Entities;
using Unity.Jobs;
using Unity.Collections;

public class ExampleSystem : SystemBase {
    private EntityQuery query;
    private NativeList<ExampleData> dataList;
    private JobHandle jobHandle;

    protected override void OnCreate() {
        query = GetEntityQuery(typeof(ExampleComponent));
        dataList = new NativeList<ExampleData>(Allocator.Persistent);
    }

    protected override void OnDestroy() {
        dataList.Dispose();
    }

    protected override void OnUpdate() {
        if (/* some condition */) {
            var chunks = query.ToArchetypeChunkArray(Allocator.TempJob, out JobHandle handle);
            var componentTypeHandle = GetComponentTypeHandle<ExampleComponent>(true);

            JobHandle job = new ProcessDataJob {
                chunks = chunks,
                componentTypeHandle = componentTypeHandle,
                dataList = dataList
            }.Schedule(handle);

            jobHandle = JobHandle.CombineDependencies(jobHandle, job);
            job.Complete();

            chunks.Dispose();
        }
    }

    [BurstCompile]
    struct ProcessDataJob : IJob {
        [ReadOnly] public NativeArray<ArchetypeChunk> chunks;
        [ReadOnly] public ComponentTypeHandle<ExampleComponent> componentTypeHandle;
        public NativeList<ExampleData> dataList;

        public void Execute() {
            foreach (var chunk in chunks) {
                var components = chunk.GetNativeArray(componentTypeHandle);
                foreach (var component in components) {
                    // Process component and update dataList
                }
            }
        }
    }

    struct ExampleData { /* ... */ }
    struct ExampleComponent : IComponentData { /* ... */ }
}
```