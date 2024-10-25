# SyncHashSet

`SyncHashSet` are sets similar to C# [HashSet](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.hashset-1) that synchronize their contents from the server to the clients.

A SyncHashSet can contain any [supported mirror type](../data-types.md)

## Usage <a href="#usage" id="usage"></a>

{% hint style="info" %}
SyncHashSet must be declared **readonly** and initialized in the constructor.
{% endhint %}

{% hint style="warning" %}
Note that by the time you wire up the Action handlers, the hashset will already be initialized, so they will not get invoked for the initial data, only updates.
{% endhint %}

```csharp
using UnityEngine;
using Mirror;

public class SyncHashSetExample : NetworkBehaviour
{
    public readonly SyncHashSet<string> namesHashSet = new SyncHashSet<string>();

    public override void OnStartClient()
    {
        // Add handlers for SyncHashSet Actions
        namesHashSet.OnAdd += OnItemAdded;
        namesHashSet.OnRemove += OnItemRemoved;
        namesHashSet.OnClear += OnHashSetCleared;

        // OnChange is a catch-all event that is called for any change
        // to the hashset. It is called after the specific events above.
        // Strongly recommended to use the specific events above instead!
        namesHashSet.OnChange += OnHashSetChanged;

        // Hashset is populated before handlers are wired up so we
        // need to manually invoke OnAdd for each element.
        foreach (string value in namesHashSet)
            namesHashSet.OnAdd.Invoke(value);
    }

    public override void OnStopClient()
    {
        // Remove handlers when client stops
        namesHashSet.OnAdd -= OnItemAdded;
        namesHashSet.OnRemove -= OnItemRemoved;
        namesHashSet.OnClear -= OnHashSetCleared;
        namesHashSet.OnChange -= OnHashSetChanged;
    }

    void OnItemAdded(string value)
    {
        Debug.Log($"Element added {value}");
    }

    void OnItemRemoved(string value)
    {
        Debug.Log($"Element removed {value}");
    }

    void OnHashSetCleared()
    {
        // OnHashSetCleared is called before the hashset is actually cleared
        // so we can iterate the hashset to get the elements if needed.
        foreach (string value in namesHashSet)
            Debug.Log($"Element cleared {value}");
    }

    // OnHashSetChanged is a catch-all event that is called for any change
    // to the hashset. It is called after the specific events above.
    //
    // NOTE: It's strongly recommended to use the specific events above instead!
    //
    // For OP_ADD, the value param is the NEW entry.
    // For OP_REMOVE, the value param is the OLD entry.
    // For OP_CLEAR, the value param is null / default.
    void OnHashSetChanged(SyncHashSet<string>.Operation op, string value)
    {
        switch (op)
        {
            case SyncHashSet<string>.Operation.OP_ADD:
                // value is the new entry
                Debug.Log($"Element added {value}");
                break;

            case SyncHashSet<string>.Operation.OP_REMOVE:
                // value is the old entry
                Debug.Log($"Element removed {value}");
                break;

            case SyncHashSet<string>.Operation.OP_CLEAR:
                // value is null / default
                // we can iterate the hashset to get the elements if needed.
                foreach (string name in namesHashSet)
                    Debug.Log($"Element cleared {name}");
                break;
        }
    }
}
```
