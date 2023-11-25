# GetterValueBinding

> `Colossal.UI.Binding.GetterValueBinding<T>`

Responsible for setting up bindings between the C# parts of the code, and the [Game UI](../game-ui.md)

## Custom Writer

If you end up using types not natively supported by `GetterValueBinding`, you can implement your own type that add support. For example, here is a `HashSetWriter` that adds support to bind a `HashSet`:

```csharp
internal class HashSetWriter<T> : IWriter<HashSet<T>> {
    [NotNull]
    private readonly IWriter<T> m_ItemWriter;

    public HashSetWriter(IWriter<T> itemWriter = null) {
        m_ItemWriter = itemWriter ?? ValueWriters.Create<T>();
    }

    public void Write(IJsonWriter writer, HashSet<T> value) {
        if (value != null) {
            writer.ArrayBegin(value.Count);
            foreach (T item in value) {
                m_ItemWriter.Write(writer, item);
            }

            writer.ArrayEnd();
            return;
        }

        writer.WriteNull();
        throw new ArgumentNullException("value", "Null passed to non-nullable hashset writer");
    }
}
```

Then for using your new writer:

```csharp
this.AddUpdateBinding(new GetterValueBinding<HashSet<string>>("namespace", "available_extensions", () => {
    return this.availableExtensions;
}, new HashSetWriter<string>()));
```