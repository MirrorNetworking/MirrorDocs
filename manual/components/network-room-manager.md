# Network Room Manager

**Please see the Room example in the Examples folder in your Mirror folder.**

The Network Room Manager is a specialized type of [Network Manager](network-manager.md) that provides a multiplayer room before entering the main play scene of the game. It allows you to set up a network with:

* A maximum player limit
* Automatic start when all players are ready
* Game is locked after start, preventing late joiners
* Customizable ways for players to choose options while in room

There are two types of player objects with the Network Room Manager:

**Room Player Prefab**

* One for each player
* Created when client connects, or player is added
* Persists until client disconnects
* Holds ready flag and configuration data
* Handles commands in the room
* Must use the [Network Room Player](network-room-player.md) component

**Player Prefab**

* One for each player
* Created when game scene is started
* Destroyed when leaving game scene
* Handles commands in the game

![](<../../.gitbook/assets/image (99).png>)

## Properties <a href="#properties" id="properties"></a>

* **Show Room GUI**\
  Show the default OnGUI controls for the room.
* **Min Players**\
  Minimum number of players needed to start a game.
* **Room Player Prefab**\
  The prefab to create for players when they enter the room (requires Network Room Player component).
* **Room Scene**\
  The scene to use for the room.
* **Gameplay Scene**\
  The scene to use for main game play.
* **pendingPlayers**\
  List that holds players that are ready to start playing.
* **roomSlots**\
  List that manages the slots for connected clients in the room.
* **allPlayersReady**\
  Bool indicating if all players are ready to start playing. This value changes as players invoke `CmdChangeReadyState` indicating true or false, and will be set false when a new client connects.

## Methods <a href="#methods" id="methods"></a>

### Server Virtual Methods <a href="#server-virtual-methods" id="server-virtual-methods"></a>

```csharp
public virtual void OnRoomStartHost() { }
public virtual void OnRoomStopHost() { }
public virtual void OnRoomStartServer() { }
public virtual void OnRoomStopServer() { }
public virtual void OnRoomServerConnect(NetworkConnectionToClient conn) { }
public virtual void OnRoomServerDisconnect(NetworkConnectionToClient conn) { }
public virtual void OnRoomServerSceneChanged(string sceneName) { }
public virtual GameObject OnRoomServerCreateRoomPlayer(NetworkConnectionToClient conn) { return null; }
public virtual GameObject OnRoomServerCreateGamePlayer(NetworkConnectionToClient conn, GameObject roomPlayer) { return null; }
public virtual void OnRoomServerAddPlayer(NetworkConnectionToClient conn) { }
public virtual bool OnRoomServerSceneLoadedForPlayer(NetworkConnectionToClient conn, GameObject roomPlayer, GameObject gamePlayer) { return true; }
public virtual void OnRoomServerPlayersReady() { }
public virtual void OnRoomServerPlayersNotReady() { }
public virtual void ReadyStatusChanged() { }
```

### Client Virtual Methods <a href="#client-virtual-methods" id="client-virtual-methods"></a>

```csharp
public virtual void OnRoomClientEnter() { }
public virtual void OnRoomClientExit() { }
public virtual void OnRoomClientConnect() { }
public virtual void OnRoomClientDisconnect() { }
public virtual void OnRoomStartClient() { }
public virtual void OnRoomStopClient() { }
public virtual void OnRoomClientSceneChanged() { }
[Obsolete("OnRoomClientReady is obsolete, use OnRoomClientDisconnect or OnRoomStopClient.")]
public virtual void OnRoomClientAddPlayerFailed() {}
public virtual void OnGUI() { }
```
