# SyncLists

SyncLists are array based lists similar to C# [List](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.list-1?view=netframework-4.7.2) that synchronize their contents from the server to the clients.

A SyncList can contain any [supported mirror type](../data-types.md).

## Differences with UNET <a href="#differences-with-hlapi" id="differences-with-hlapi"></a>

UNET also supports SyncLists, but we have redesigned them to make them more efficient and easier to use. Some of the key differences include:

* In UNET, SyncLists were synchronized immediately when they changed. If you add 10 elements, that means 10 separate messages. Mirror synchronizes SyncLists with the SyncVars. The 10 elements and other SyncVars are batched together into a single message. Mirror also respects the sync interval when synchronizing lists.
* In UNET if you want a list of structs, you have to use `SyncListStruct`, we changed it to just `SyncList`
* In UNET the Callback is a delegate. In Mirror we changed it to an event, so that you can add many subscribers.
* In UNET the Callback tells you the operation and index. In Mirror, the callback also receives an item. We made this change so that we could tell what item was removed.
* In UNET you must create a class that inherits from SyncList. In Mirror you can just use SyncList directly (starting with version 20.0.0)

## Usage <a href="#usage" id="usage"></a>

Add a SyncList field to your NetworkBehaviour class.

{% hint style="info" %}
SyncList must be declared **readonly** and initialized in the constructor.
{% endhint %}

```csharp
public struct Item
{
    public string name;
    public int amount;
    public Color32 color;
}

public class Player : NetworkBehaviour
{
    public readonly SyncList<Item> inventory = new SyncList<Item>();

    public int coins = 100;

    [Command]
    public void CmdPurchase(string itemName)
    {
        if (coins > 10)
        {
            coins -= 10;
            Item item = new Item
            {
                name = "Sword",
                amount = 3,
                color = new Color32(125, 125, 125, 255)
            };

            // during next synchronization,  all clients will see the item
            inventory.Add(item);
        }
    }
}
```

You can also detect when a SyncList changes in the client or server. This is useful for refreshing your character when you add equipment or determining when you need to update your database. Subscribe to the Callback event typically during `Start`, `OnClientStart`, or `OnServerStart` for that.

{% hint style="warning" %}
Note that by the time you subscribe, the list will already be populated, so you will not get a call for the initial data, only updates.
{% endhint %}

```csharp
class Player : NetworkBehaviour {

    public override void OnStartClient()
    {
        inventory.Callback += OnInventoryUpdated;
        
        // Process initial SyncList payload
        for (int index = 0; index < inventory.Count; index++)
            OnInventoryUpdated(SyncList<Item>.Operation.OP_ADD, index, new Item(), inventory[index]);
    }

    void OnInventoryUpdated(SyncList<Item>.Operation op, int index, Item oldItem, Item newItem)
    {
        switch (op)
        {
            case SyncList<Item>.Operation.OP_ADD:
                // index is where it was added into the list
                // newItem is the new item
                break;
            case SyncList<Item>.Operation.OP_INSERT:
                // index is where it was inserted into the list
                // newItem is the new item
                break;
            case SyncList<Item>.Operation.OP_REMOVEAT:
                // index is where it was removed from the list
                // oldItem is the item that was removed
                break;
            case SyncList<Item>.Operation.OP_SET:
                // index is of the item that was changed
                // oldItem is the previous value for the item at the index
                // newItem is the new value for the item at the index
                break;
            case SyncList<Item>.Operation.OP_CLEAR:
                // list got cleared
                break;
        }
    }
}
```

