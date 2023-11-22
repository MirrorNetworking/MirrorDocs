# Network Room Player

The Network Room Player stores per-player state for the [Network Room Manager](network-room-manager.md) while in the room. When using this component, you need to write a script which allows players to indicate they are ready to begin playing, which sets the ReadyToBegin property.

A game object with a Network Room Player component must also have a Network Identity component. When you create a Network Room Player component on a game object, Unity also creates a Network Identity component on that game object if it does not already have one.

![](<../../.gitbook/assets/image (80).png>)

* **Show Room GUI**\
  Enable this to show the developer GUI for players in the room. This UI is only intended to be used for ease of development. This is enabled by default.
* **Ready To Begin**\
  Diagnostic indicator that a player is Ready.
* **Index**\
  Diagnostic index of the player, e.g. Player 1, Player 2, etc.
* **Network Sync Interval**\
  The rate at which information is sent from the Network Room Player to the server.

## Methods <a href="#methods" id="methods"></a>

### Client Virtual SyncVar Hook Methods <a href="#client-virtual-syncvar-methods" id="client-virtual-syncvar-methods"></a>

```csharp
public virtual void IndexChanged(int oldIndex, int newIndex) { }
public virtual void ReadyStateChanged(bool oldReadyState, bool newReadyState) { }

```

### Client Virtual Methods <a href="#client-virtual-methods" id="client-virtual-methods"></a>

```csharp
public virtual void OnClientEnterRoom() { }
public virtual void OnClientExitRoom() { }
[Obsolete("OnClientReady is deprecated, use the SyncVar ReadyStateChanged hook instead.")]
public virtual void OnClientReady(bool readyState) {}
```
