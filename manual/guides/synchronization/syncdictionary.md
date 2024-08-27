# SyncDictionary

A SyncDictionary is an associative array containing an unordered list of key, value pairs. Keys and values can be any [supported mirror type](../data-types.md). By default we use .Net [Dictionary](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.dictionary-2?view=netcore-3.1) which may impose additional constraints on the keys and values.

SyncDictionary works much like [SyncLists](synclists.md): when you make a change on the server the change is propagated to all clients and the Callback is called. Only deltas are transmitted.

## Usage <a href="#usage" id="usage"></a>

Add a field to your NetworkBehaviour class of type `SyncDictionary`.

{% hint style="info" %}
`SyncDictionary must` be declared **readonly** and initialized in the constructor.
{% endhint %}

{% hint style="warning" %}
Note that by the time you subscribe to the callback, the dictionary will already be initialized, so you will not get a call for the initial data, only updates.
{% endhint %}

## Simple Example <a href="#simple-example" id="simple-example"></a>

```csharp
using UnityEngine;
using Mirror;

public struct Item
{
    public string name;
    public int hitPoints;
    public int durability;
}

public class ExamplePlayer : NetworkBehaviour
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
        // Equipment is already populated with anything the server set up
        // but we can subscribe to the callback in case it is updated later on
        Equipment.Callback += OnEquipmentChange;

        // Process initial SyncDictionary payload
        foreach (KeyValuePair<string, Item> kvp in Equipment)
            OnEquipmentChange(SyncDictionary<string, Item>.Operation.OP_ADD, kvp.Key, kvp.Value);
    }

    void OnEquipmentChange(SyncDictionary<string, Item>.Operation op, string key, Item item)
    {
        switch (op)
        {
            case SyncIDictionary<string, Item>.Operation.OP_ADD:
                // entry added
                break;
            case SyncIDictionary<string, Item>.Operation.OP_SET:
                // entry changed
                // "Item item" is the OLD value
                // Get the new value from 'Equipment[key]'
                break;
            case SyncIDictionary<string, Item>.Operation.OP_REMOVE:
                // entry removed
                // "Item item" is the OLD value
                break;
            case SyncIDictionary<string, Item>.Operation.OP_CLEAR:
                // Dictionary was cleared
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
