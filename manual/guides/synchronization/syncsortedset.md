# SyncSortedSet

`SyncSortedSet` are sets similar to C# [SortedSet](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.sortedset-1) that synchronize their contents from the server to the clients.

Unlike SyncHashSets, all elements in a SyncSortedSet are sorted when they are inserted. Please note this has some performance implications.

A SyncSortedSet can contain any [supported mirror type](../data-types.md)

## Usage <a href="#usage" id="usage"></a>

{% hint style="info" %}
SyncSortedSet must be declared **readonly** and initialized in the constructor.
{% endhint %}

{% hint style="warning" %}
Note that by the time you wire up the Action handlers, the sorted set will already be initialized, so they will not get invoked for the initial data, only updates.
{% endhint %}

```csharp
using UnityEngine;
using Mirror;

public class SyncSortedSetExample : NetworkBehaviour
{
    public readonly SyncSortedSet<string> namesSortedSet = new SyncSortedSet<string>();

    public override void OnStartClient()
    {
        // Add handlers for SyncSortedSet Actions
        namesSortedSet.OnAdd += OnItemAdded;
        namesSortedSet.OnRemove += OnItemRemoved;
        namesSortedSet.OnClear += OnSortedSetCleared;

        // OnChange is a catch-all event that is called for any change
        // to the SortedSet. It is called after the specific events above.
        // Strongly recommended to use the specific events above instead!
        namesSortedSet.OnChange += OnSortedSetChanged;

        // SortedSet is populated before handlers are wired up so we
        // need to manually invoke OnAdd for each element.
        foreach (string value in namesSortedSet)
            namesSortedSet.OnAdd.Invoke(value);
    }

    public override void OnStopClient()
    {
        // Remove handlers when client stops
        namesSortedSet.OnAdd -= OnItemAdded;
        namesSortedSet.OnRemove -= OnItemRemoved;
        namesSortedSet.OnClear -= OnSortedSetCleared;
        namesSortedSet.OnChange -= OnSortedSetChanged;
    }

    void OnItemAdded(string value)
    {
        Debug.Log($"Element added {value}");
    }

    void OnItemRemoved(string value)
    {
        Debug.Log($"Element removed {value}");
    }

    void OnSortedSetCleared()
    {
        // OnSortedSetCleared is called before the SortedSet is actually cleared
        // so we can iterate the SortedSet to get the elements if needed.
        foreach (string value in namesSortedSet)
            Debug.Log($"Element cleared {value}");
    }

    // OnSortedSetChanged is a catch-all event that is called for any change
    // to the SortedSet. It is called after the specific events above.
    //
    // NOTE: It's strongly recommended to use the specific events above instead!
    //
    // For OP_ADD, the value param is the NEW entry.
    // For OP_REMOVE, the value param is the OLD entry.
    // For OP_CLEAR, the value param is null / default.
    void OnSortedSetChanged(SyncSortedSet<string>.Operation op, string value)
    {
        switch (op)
        {
            case SyncSortedSet<string>.Operation.OP_ADD:
                // value is the new entry
                Debug.Log($"Element added {value}");
                break;

            case SyncSortedSet<string>.Operation.OP_REMOVE:
                // value is the old entry
                Debug.Log($"Element removed {value}");
                break;

            case SyncSortedSet<string>.Operation.OP_CLEAR:
                // value is null / default
                // we can iterate the SortedSet to get the elements if needed.
                foreach (string name in namesSortedSet)
                    Debug.Log($"Element cleared {name}");
                break;
        }
    }
}
```
