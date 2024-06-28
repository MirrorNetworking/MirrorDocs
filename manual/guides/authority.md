# Authority

## Network Authority <a href="#network-authority" id="network-authority"></a>

Authority is a way of deciding who owns an object and has control over it.

### Server Authority <a href="#server-authority" id="server-authority"></a>

Server authority means that the server has control of an object. Server has authority over an object by default. This means the server would manage and control the collectible items, moving platforms, NPCs, and any other networked objects that aren't the player.

### Client Authority <a href="#client-authority" id="client-authority"></a>

Client authority means that the client has control of an object.

When a client has authority over an object it means that they can call [Commands](communications/remote-actions.md#commands) and that the object will automatically be destroyed when the client disconnects.

Even if a client has authority over an object the server still controls SyncVar and control other serialization features. A component will need to use a [Commands](communications/remote-actions.md#commands) to update the state on the server in order for it to sync to other clients.

### How to give authority <a href="#how-to-give-authority" id="how-to-give-authority"></a>

By default, the server has Authority over all objects. The server can give authority to objects that a client needs to control, like the player object.

If you spawn a player object using `NetworkServer.AddPlayerForConnection` then it will automatically be given authority.

#### Using NetworkServer.Spawn <a href="#using-networkserverspawn" id="using-networkserverspawn"></a>

You can give authority to a client when an object is spawned. This is done by passing in the connection to the spawn message

```csharp
GameObject go = Instantiate(prefab);
NetworkServer.Spawn(go, connectionToClient);
```

{% hint style="info" %}
Note: A change of NetworkServer.Spawn hierarchy is not automatically synced.
{% endhint %}

#### Using identity.AssignClientAuthority <a href="#using-identityassignclientauthority" id="using-identityassignclientauthority"></a>

You can give authority to a client any time using `AssignClientAuthority`. This can be done by calling `AssignClientAuthority` on the object you want to give authority to.

```csharp
identity.AssignClientAuthority(conn);
```

You may want to do this when a player picks up an item

```csharp
// Command on player object
void CmdPickupItem(NetworkIdentity item)
{
    item.AssignClientAuthority(connectionToClient); 
}
```

### How to remove authority <a href="#how-to-remove-authority" id="how-to-remove-authority"></a>

You can use `identity.RemoveClientAuthority` to remove client authority from an object.

```csharp
identity.RemoveClientAuthority();
```

Authority can't be removed from the player object. Instead, you will have to replace the player object using `NetworkServer.ReplacePlayerForConnection`.

### On Authority <a href="#on-authority" id="on-authority"></a>

When authority is given to or removed from an object a message will be sent to that client to notify them. This will cause the `OnStartAuthority` or `OnStopAuthority` functions to be called.

### Check Authority <a href="#check-authority" id="check-authority"></a>

#### Client Side <a href="#client-side" id="client-side"></a>

The `identity.isOwned` property can be used to check if the local player has authority over an object.

#### Server Side <a href="#server-side" id="server-side"></a>

The `identity.connectionToClient` property can be checked to see which client has authority over an object. If it is null then the server has authority.
