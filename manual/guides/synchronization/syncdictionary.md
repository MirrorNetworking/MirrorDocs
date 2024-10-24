# SyncDictionary

A SyncDictionary is an associative array containing an unordered list of key, value pairs. Keys and values can be any [supported mirror type](../data-types.md). By default we use .Net [Dictionary](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.dictionary-2?view=netcore-3.1) which may impose additional constraints on the keys and values.

SyncDictionary works much like [SyncLists](synclists.md): when you make a change on the server the change is propagated to all clients and the appropriate Actions are invoked. Only deltas are transmitted.

## Usage <a href="#usage" id="usage"></a>

{% hint style="info" %}
`SyncDictionary must` be declared **readonly** and initialized in the constructor.
{% endhint %}

{% hint style="warning" %}
Note that by the time you subscribe to the callback, the dictionary will already be initialized, so you will not get a call for the initial data, only updates.
{% endhint %}

## Simple Example <a href="#simple-example" id="simple-example"></a>

```csharp
public struct Item
{
    public string name;
    public int hitPoints;
    public int durability;

    public override string ToString()
    {
        return $"name={name} hitPoints={hitPoints} durability={durability}";
    }
}

public class SyncDictionaryExample : NetworkBehaviour
{
    public readonly SyncDictionary<string, Item> Equipment = new SyncDictionary<string, Item>();

    public override void OnStartServer()
    {
        Equipment.Add("head", new Item { name = "Helmet", hitPoints = 10, durability = 20 });
        Equipment.Add("body", new Item { name = "Epic Armor", hitPoints = 50, durability = 50 });
        Equipment.Add("feet", new Item { name = "Sneakers", hitPoints = 3, durability = 40 });
        Equipment.Add("hands", new Item { name = "Sword", hitPoints = 30, durability = 15 });
    }

    public override void OnStartClient()
    {
        // Add handlers for SyncDictionary Actions
        Equipment.OnAdd += OnItemAdded;
        Equipment.OnSet += OnItemSet;
        Equipment.OnRemove += OnItemRemoved;
        Equipment.OnClear += OnDictionaryCleared;

        // OnChange is a catch-all event that is called for any change
        // to the dictionary. It is called after the specific events above.
        // Strongly recommended to use the specific events above instead!
        Equipment.OnChange += OnDictionaryChanged;

        // Dictionary is populated before handlers are wired up so we
        // need to manually invoke OnAdd for each element.
        foreach (string key in Equipment.Keys)
            Equipment.OnAdd.Invoke(key);
    }

    void OnItemAdded(string key)
    {
        Debug.Log($"Element added {key} {Equipment[key]}");
    }

    void OnItemSet(string key, Item oldValue)
    {
        Debug.Log($"Element set {key} from {oldValue} to {Equipment[key]}");
    }

    void OnItemRemoved(string key, Item oldValue)
    {
        Debug.Log($"Element removed {key} {oldValue}");
    }

    void OnDictionaryCleared()
    {
        // OnDictionaryCleared is called before the dictionary is actually cleared
        // so we can iterate the dictionary to get the elements if needed.
        foreach (KeyValuePair<string, Item> kvp in Equipment)
            Debug.Log($"Element cleared {kvp.Key} {kvp.Value}");
    }

    // OnDictionaryChanged is a catch-all event that is called for any change
    // to the dictionary. It is called after the specific events above.
    //
    // NOTE: It's strongly recommended to use the specific events above instead!
    //
    // For OP_ADD, the value param is the NEW entry.
    // For OP_SET, the value param is the OLD entry.
    // For OP_REMOVE, the value param is the OLD entry.
    // For OP_CLEAR, the value param is null / default.
    void OnDictionaryChanged(SyncDictionary<string, Item>.Operation op, string key, Item value)
    {
        switch (op)
        {
            case SyncDictionary<string, Item>.Operation.OP_ADD:
                // value is the new entry
                Debug.Log($"Element added {key} {value}");
                break;

            case SyncDictionary<string, Item>.Operation.OP_SET:
                // value is the old entry
                Debug.Log($"Element set {key} from {value} to {Equipment[key]}");
                break;

            case SyncDictionary<string, Item>.Operation.OP_REMOVE:
                // value is the old entry
                Debug.Log($"Element removed {key} {value}");
                break;

            case SyncDictionary<string, Item>.Operation.OP_CLEAR:
                // value is null / default
                // we can iterate the dictionary to get the elements if needed.
                foreach (KeyValuePair<string, Item> kvp in Equipment)
                    Debug.Log($"Element cleared {kvp.Key} {kvp.Value}");
                break;
        }
    }
}
```

By default, SyncDictionary uses a [Dictionary](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.dictionary-2?view=netcore-3.1) to store it's data. If you want to use a different `IDictionary` implementation such as [SortedList](https://docs.microsoft.com/en-us/dotnet/api/system.collections.sortedlist?view=netcore-3.1) or [SortedDictionary](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.sorteddictionary-2?view=netcore-3.1), then use `SyncIDictionary` and pass the dictionary instance you want it to use. For example:

```csharp
public class ExamplePlayer : NetworkBehaviour
{
    public readonly SyncIDictionary Equipment = 
        new SyncIDictionary(new SortedList());
}
```
