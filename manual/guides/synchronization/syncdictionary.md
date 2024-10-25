# SyncDictionary

A SyncDictionary is an associative array containing an unordered list of key, value pairs. Keys and values can be any [supported mirror type](../data-types.md). By default we use .Net [Dictionary](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.dictionary-2?view=netcore-3.1) which may impose additional constraints on the keys and values.

SyncDictionary works much like [SyncLists](synclists.md): when you make a change on the server the change is propagated to all clients and the appropriate Actions are invoked. Only deltas are transmitted.

## Usage <a href="#usage" id="usage"></a>

{% hint style="info" %}
`SyncDictionary must` be declared **readonly** and initialized in the constructor.
{% endhint %}

{% hint style="warning" %}
Note that by the time you wire up the Action handlers, the dictionary will already be initialized, so they will not get invoked for the initial data, only updates.
{% endhint %}

## Simple Example <a href="#simple-example" id="simple-example"></a>

```csharp
using System.Collections.Generic;
using UnityEngine;
using Mirror;

public enum Slots : byte { head, body, feet, hands }

public struct Item
{
    public string name;
    public int hitPoints;
    public int durability;

    public Item(string name, int hitPoints, int durability)
    {
        this.name = name;
        this.hitPoints = hitPoints;
        this.durability = durability;
    }

    public override string ToString()
    {
        return $"name={name} hitPoints={hitPoints} durability={durability}";
    }
}

public class SyncDictionaryExample : NetworkBehaviour
{
    public readonly SyncDictionary<Slots, Item> Equipment = new SyncDictionary<Slots, Item>();

    public override void OnStartServer()
    {
        Equipment[Slots.head] = new Item("Helmet", 10, 20);
        Equipment[Slots.body] = new Item("Epic Armor", 50, 50);
        Equipment[Slots.feet] = new Item("Sneakers", 3, 40);
        Equipment[Slots.hands] = new Item("Sword", 30, 15);
    }

    public override void OnStartClient()
    {
        // Add handlers for SyncDictionary Actions
        Equipment.OnAdd += OnItemAdded;
        Equipment.OnSet += OnItemChanged;
        Equipment.OnRemove += OnItemRemoved;
        Equipment.OnClear += OnDictionaryCleared;

        // OnChange is a catch-all event that is called for any change
        // to the dictionary. It is called after the specific events above.
        // Strongly recommended to use the specific events above instead!
        Equipment.OnChange += OnDictionaryChanged;

        // Dictionary is populated before handlers are wired up so we
        // need to manually invoke OnAdd for each element.
        foreach (Slots key in Equipment.Keys)
            Equipment.OnAdd.Invoke(key);
    }

    public override void OnStopClient()
    {
        // Remove handlers when client stops
        Equipment.OnAdd -= OnItemAdded;
        Equipment.OnSet -= OnItemChanged;
        Equipment.OnRemove -= OnItemRemoved;
        Equipment.OnClear -= OnDictionaryCleared;
        Equipment.OnChange -= OnDictionaryChanged;
    }

    void OnItemAdded(Slots key)
    {
        Debug.Log($"Element added {key} {Equipment[key]}");
    }

    void OnItemChanged(Slots key, Item oldValue)
    {
        Debug.Log($"Element changed {key} from {oldValue} to {Equipment[key]}");
    }

    void OnItemRemoved(Slots key, Item oldValue)
    {
        Debug.Log($"Element removed {key} {oldValue}");
    }

    void OnDictionaryCleared()
    {
        // OnDictionaryCleared is called before the dictionary is actually cleared
        // so we can iterate the dictionary to get the elements if needed.
        foreach (KeyValuePair<Slots, Item> kvp in Equipment)
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
    void OnDictionaryChanged(SyncDictionary<Slots, Item>.Operation op, Slots key, Item value)
    {
        switch (op)
        {
            case SyncDictionary<Slots, Item>.Operation.OP_ADD:
                // value is the new entry
                Debug.Log($"Element added {key} {value}");
                break;

            case SyncDictionary<Slots, Item>.Operation.OP_SET:
                // value is the old entry
                Debug.Log($"Element set {key} from {value} to {Equipment[key]}");
                break;

            case SyncDictionary<Slots, Item>.Operation.OP_REMOVE:
                // value is the old entry
                Debug.Log($"Element removed {key} {value}");
                break;

            case SyncDictionary<Slots, Item>.Operation.OP_CLEAR:
                // value is null / default
                // we can iterate the dictionary to get the elements if needed.
                foreach (KeyValuePair<Slots, Item> kvp in Equipment)
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
