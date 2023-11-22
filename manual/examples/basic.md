# Basic

The Basic Example illustrates how to manage UI objects from the Player object using locally instantiated `PlayerUI` prefabs with [SyncVars](../guides/synchronization/syncvars.md) and Events

<div align="left">

<img src="../../.gitbook/assets/image (2).png" alt="">

</div>

The scene Canvas has a `CanvasUI` script with references to its children:

<div align="left">

<img src="../../.gitbook/assets/image (4).png" alt="Scene Canvas">

</div>

The `PlayerUI` prefab is a UI fragment that has a `PlayerUI` script with references to its own children:

<div align="left">

<img src="../../.gitbook/assets/image (36).png" alt="PlayerUI Prefab">

</div>

The Player script on the Player object has a reference to the `PlayerUI` prefab and three `SyncVars`:

<div align="left">

<img src="../../.gitbook/assets/image (19).png" alt="Player Object">

</div>

The Player script also has three events that are invoked by the [SyncVar hooks](../guides/synchronization/syncvar-hooks.md):

```
public event System.Action<int> OnPlayerNumberChanged;
public event System.Action<Color32> OnPlayerColorChanged;
public event System.Action<int> OnPlayerDataChanged;
```

When the Player object is spawned on the client, a `PlayerUI` is instantiated as a child of the `PlayersPanel` in the Canvas via the references provided in the `CanvasUI` script, and the `SetPlayer` method is called with the corresponding Player script reference.  The `PlayerUI` script subscribes to the events above, and updates its UI elements as the `SyncVars` are updated from the server.
