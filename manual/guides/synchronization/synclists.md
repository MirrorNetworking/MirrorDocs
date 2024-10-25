# SyncLists

SyncLists are array based lists similar to C# [List](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.list-1?view=netframework-4.7.2) that synchronize their contents from the server to the clients.

A SyncList can contain any [supported mirror type](../data-types.md).

## Usage <a href="#usage" id="usage"></a>

{% hint style="info" %}
SyncList must be declared **readonly** and initialized in the constructor.
{% endhint %}

{% hint style="warning" %}
Note that by the time you wire up the Action handlers, the list will already be initialized, so they will not get invoked for the initial data, only updates.
{% endhint %}

```csharp
using UnityEngine;
using Mirror;

public class SyncListExample : NetworkBehaviour
{
    public readonly SyncList<string> namesList = new SyncList<string>();

    public override void OnStartClient()
    {
        // Add handlers for SyncList Actions
        namesList.OnAdd += OnItemAdded;
        namesList.OnInsert += OnItemInserted;
        namesList.OnSet += OnItemChanged;
        namesList.OnRemove += OnItemRemoved;
        namesList.OnClear += OnListCleared;

        // OnChange is a catch-all event that is called for any change
        // to the list. It is called after the specific events above.
        // Strongly recommended to use the specific events above instead!
        namesList.OnChange += OnListChanged;

        // List is populated before handlers are wired up so we
        // need to manually invoke OnAdd for each element.
        for (int i = 0; i < namesList.Count; i++)
            namesList.OnAdd.Invoke(i);
    }

    public override void OnStopClient()
    {
        // Remove handlers when client stops
        namesList.OnAdd -= OnItemAdded;
        namesList.OnInsert -= OnItemInserted;
        namesList.OnSet -= OnItemChanged;
        namesList.OnRemove -= OnItemRemoved;
        namesList.OnClear -= OnListCleared;
        namesList.OnChange -= OnListChanged;
    }

    void OnItemAdded(int index)
    {
        Debug.Log($"Element added at index {index} {namesList[index]}");
    }

    void OnItemInserted(int index)
    {
        Debug.Log($"Element inserted at index {index} {namesList[index]}");
    }

    void OnItemChanged(int index, string oldValue)
    {
        Debug.Log($"Element changed at index {index} from {oldValue} to {namesList[index]}");
    }

    void OnItemRemoved(int index, string oldValue)
    {
        Debug.Log($"Element removed at index {index} {oldValue}");
    }

    void OnListCleared()
    {
        // OnListCleared is called before the list is actually cleared
        // so we can iterate the list to get the elements if needed.
        foreach (string name in namesList)
            Debug.Log($"Element cleared {name}");
    }

    // OnListChanged is a catch-all event that is called for any change
    // to the list. It is called after the specific events above.
    //
    // NOTE: It's strongly recommended to use the specific events above instead!
    //
    // For OP_ADD and OP_INSERT, the value param is the NEW entry.
    // For OP_SET and OP_REMOVEAT, the value param is the OLD entry.
    // For OP_CLEAR, the param is null / default but you can iterate the list.
    void OnListChanged(SyncList<string>.Operation op, int index, string value)
    {
        switch (op)
        {
            case SyncList<string>.Operation.OP_ADD:
                // value is the NEW value of the entry
                Debug.Log($"Element added at index {index} {value}");
                break;

            case SyncList<string>.Operation.OP_INSERT:
                // value is the NEW value of the entry
                Debug.Log($"Element inserted at index {index} {value}");
                break;

            case SyncList<string>.Operation.OP_SET:
                // value is the OLD value of the entry
                Debug.Log($"Element changed at index {index} from {value} to {namesList[index]}");
                break;

            case SyncList<string>.Operation.OP_REMOVEAT:
                // value is the OLD value of the entry
                Debug.Log($"Element removed at index {index} was {value}");
                break;

            case SyncList<string>.Operation.OP_CLEAR:
                // value is null / default
                // we can iterate the list to get the elements if needed.
                foreach (string name in namesList)
                    Debug.Log($"Element cleared {name}");
                break;
        }
    }
}
```

