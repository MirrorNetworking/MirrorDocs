# Deprecations

Certain features of Unity Networking (UNet) were removed from Mirror or modified for various reasons. This page will identify all changed and removed features, properties, and methods, the reason for change or removal, and possible alternatives.

{% hint style="info" %}
**Note:** Some changes in this document may apply to an upcoming release to the Asset Store.
{% endhint %}

## Match Namespace & Host Migration <a href="#match-namespace--host-migration" id="match-namespace--host-migration"></a>

As part of the Unity Services, this entire namespace was removed. It didn't work well to begin with, and was incredibly complex to be part of the core networking package. We expect this, along with other back-end services, will be provided through standalone apps that have integration to Mirror.

## Network Server Simple <a href="#network-server-simple" id="network-server-simple"></a>

This was too complex and impractical to maintain for what little it did, and was removed. There are much easier ways to make a basic listen server, with or without one of our transports.

## Couch Co-Op <a href="#couch-co-op" id="couch-co-op"></a>

The core networking was greatly simplified by removing this low-hanging fruit. It was buggy, and too convoluted to be worth fixing. For those that need something like this, consider defining a non-visible player prefab as a message conduit that spawns actual player prefabs with client authority. All inputs would route through the conduit prefab to control the player objects.

## Message Types <a href="#message-types" id="message-types"></a>

The `MsgType` enumeration was removed. All message types are generated dynamically. Use `Send` instead.

## Network Transform <a href="#network-transform" id="network-transform"></a>

[Network Transform](../components/network-transform/) was fully replaced so that it only syncs position, rotation and scale, has control options for those and whether to interpolate them, and uses Snapshot Interpolation. It also has a bunch of virtual methods and there's a [Script Template](script-templates.md) for making your own derived version. Rigidbody support was removed in favor of a separate [Network Rigidbody](../components/network-rigidbody.md) component.

## Network Animator <a href="#network-animator" id="network-animator"></a>

[Network Animator](../components/network-animator.md) was also simplified, as it batches all Animator parameters into a single update message.

## SyncVar Hook Parameters <a href="#syncvar-hook-parameters" id="syncvar-hook-parameters"></a>

[SyncVar](../guides/synchronization/syncvars.md) property values are now updated before the hook is called, and hooks now require two parameters of the same type as the property: `oldValue` and `newValue`

## SyncListSTRUCT <a href="#syncliststruct" id="syncliststruct"></a>

Use [SyncList](../guides/synchronization/synclists.md) instead.

## SyncList Classes

* `SyncListString` was replaced by `SyncList<string>` .
* `SyncListFloat` was replaced by `SyncList<float>`.
* `SyncListInt` was replaced by `SyncList<int>`.
* `SyncListUInt` was replaced by `SyncList<uint>`.
* `SyncListBool` was replaced by `SyncList<bool>`.

See [documentation](../guides/synchronization/synclists.md) for more details.

## SyncList Operations <a href="#synclist-operations" id="synclist-operations"></a>

* `OP_REMOVE` was replaced by `OP_REMOVEAT`
* `OP_DIRTY` was replaced by `OP_SET`

See [documentation](../guides/synchronization/synclists.md) for more details.

## SyncDictionary Operations <a href="#syncidictionary-operations" id="syncidictionary-operations"></a>

* `OP_DIRTY` was replaced by `OP_SET`

See [documentation](../guides/synchronization/syncdictionary.md) for more details.

## SyncObject

* This is now a class instead of an interface.
* `Flush` - Use `ClearChanges` instead.

## Quality of Service Flags <a href="#quality-of-service-flags" id="quality-of-service-flags"></a>

In classic UNet, QoS Flags were used to determine how packets got to the remote end. For example, if you needed a packet to be prioritized in the queue, you would specify a high priority flag which the Unity LLAPI would then receive and deal with appropriately. Unfortunately, this caused a lot of extra work for the transport layer and some of the QoS flags did not work as intended due to buggy code that relied on too much magic.

In Mirror, QoS flags were replaced with a "Channels" system. While the default transport, [Telepathy](../transports/telepathy-transport.md), does not use channels at all because it's TCP-based, other transports, such as [Ignorance](../transports/ignorance.md) and [LiteNetLib](../transports/litenetlib-transport.md), do support them.

The currently defined channels are:

* `Channels.Reliable = 0`
* `Channels.Unreliable = 1`

## Changes by Class <a href="#changes-by-class" id="changes-by-class"></a>

### NetworkManager <a href="#networkmanager" id="networkmanager"></a>

* `NetworkConnection` was replaced by `NetworkConnectionToClient` in many places.
* `networkPort`\
  Removed as part of separating Transports to components. Not all transports use ports, but those that do have a field for it. See [Transports](../transports/) for more info.
* `IsHeadless()`\
  Use compiler symbol `UNITY_SERVER` instead.
* `ConfigureServerFrameRate` was renamed to `ConfigureHeadlessFrameRate`.
* `client`\
  Use NetworkClient directly, it will be made static soon. For example, use `NetworkClient.Send(message)` instead of `NetworkManager.client.Send(message)`.
* `IsClientConnected()`\
  Use static property `NetworkClient.isConnected` instead.
* `onlineScene` and `offlineScene`\
  &#x20;These store full paths now, so use SceneManager.GetActiveScene().path instead.
* `OnStartClient(NetworkClient client)`\
  &#x20;Override OnStartClient() instead since all `NetworkClient` methods are static now.
* `OnClientChangeScene(string newSceneName)`\
  &#x20;Override `OnClientChangeScene(string newSceneName, SceneOperation sceneOperation, bool customHandling)` instead.
* `OnClientChangeScene(string newSceneName, SceneOperation sceneOperation)`\
  &#x20;Override `OnClientChangeScene(string newSceneName, SceneOperation sceneOperation, bool customHandling)` instead.
* `OnServerAddPlayer(NetworkConnection conn, AddPlayerMessage extraMessage)`\
  &#x20;Override `OnServerAddPlayer(NetworkConnection conn)` instead. See [Custom Player Spawn Guide](../guides/gameobjects/custom-character-spawning.md) for details.
* `OnServerRemovePlayer(NetworkConnection conn, NetworkIdentity player)`\
  &#x20;Use `NetworkServer.RemovePlayerForConnection(NetworkConnection conn, GameObject player, bool keepAuthority = false)` instead.
*   `OnServerError(NetworkConnection conn, int errorCode)`

    Replaced with `OnServerError(NetworkConnection conn, Exception exception)`.
*   `OnClientError(NetworkConnection conn, int errorCode)`

    Replaced with `OnClientError(Exception exception)`.
* `disconnectInactiveConnections` and `disconnectInactiveTimeout` were removed.
* OnClient\* virtual methods no longer take a `NetworkConnection` parameter. Remove the parameter from your overrides and use `NetworkClient.connection` in your code instead.
* `serverTickRate` renamed to `sendRate`.
* `serverTickInterval` moved to `NetworkServer`.

### NetworkManagerHUD

*   `showGUI` was removed.

    Disable the component instead.

### NetworkRoomManager <a href="#networkroommanager" id="networkroommanager"></a>

* `NetworkConnection` was replaced by `NetworkConnectionToClient` in many places.
* `OnRoomServerCreateGamePlayer(NetworkConnection conn)`\
  &#x20;Use `OnRoomServerCreateGamePlayer(NetworkConnection conn, GameObject roomPlayer)` instead.
* `OnRoomServerSceneLoadedForPlayer(GameObject roomPlayer, GameObject gamePlayer)`\
  &#x20;Use `OnRoomServerSceneLoadedForPlayer(NetworkConnection conn, GameObject roomPlayer, GameObject gamePlayer)` instead.
*   Client virtual methods no longer take a `NetworkConnection` parameter.

    Use `NetworkClient.connection` within your overrides.

### NetworkIdentity <a href="#networkidentity" id="networkidentity"></a>

* `clientAuthorityOwner`\
  &#x20;Use connectionToClient instead
* `GetSceneIdenity`\
  &#x20;Use `GetSceneIdentity` instead (typo in original name)
* `RemoveClientAuthority(NetworkConnection conn)`\
  &#x20;NetworkConnection parameter is no longer needed and nothing is returned
*   `spawned` dictionary

    This has been split up to `NetworkServer.spawned` and `NetworkClient.spawned` dictionaries.
* Local Player Authority checkbox\
  &#x20;This checkbox is no longer needed, and we simplified how [Authority](../guides/authority.md) works in Mirror.

### NetworkBehaviour <a href="#networkbehaviour" id="networkbehaviour"></a>

* `NetworkConnection` was replaced by `NetworkConnectionToClient` in many places.
* `sendInterval` attribute\
  &#x20;Use `NetworkBehaviour.syncInterval` field instead. Can be modified in the Inspector too.
* `List m_SyncObjects`\
  &#x20;Use `List syncObjects` instead.
* `OnSetLocalVisibility(bool visible)`\
  &#x20;Override `OnSetHostVisibility(bool visible)` instead.
* `OnRebuildObservers`, `OnCheckObserver`, and `OnSetHostVisibility` were moved to a separate class called `NetworkVisibility`
* `NetworkBehaviour.OnNetworkDestroy` was renamed to `NetworkBehaviour.OnStopClient`.
* `getSyncVarHookGuard` renamed to `GetSyncVarHookGuard`.
* `setSyncVarHookGuard` - renamed to `SetSyncVarHookGuard`.
* `SetDirtyBit` - Use `SetSyncVarDirtyBit` instead.
* `[Command]` attribute parameter `ignoreAuthority` replaced with `requiresAuthority`.
* `[ClientRpc]` attribute parameter `includeOwner` replace with `excludeOwner`.
* `hasAuthority` renamed to `isOwned`.

### NetworkConnection <a href="#networkconnection" id="networkconnection"></a>

* `hostId`\
  &#x20;Removed because it's not needed ever since we removed LLAPI as default. It's always 0 for regular connections and -1 for local connections. Use `connection.GetType() == typeof(NetworkConnection)` to check if it's a regular or local connection.
* `isConnected`\
  &#x20;Removed because it's pointless. A `NetworkConnection` is always connected.
* `InvokeHandlerNoData(int msgType)`\
  &#x20;Use `InvokeHandler` instead.
* `playerController`\
  &#x20;renamed to `identity` since that's what it is: the `NetworkIdentity` for the connection.
* `RegisterHandler(short msgType, NetworkMessageDelegate handler)`\
  &#x20;Use `NetworkServer.RegisterHandler()` or `NetworkClient.RegisterHandler()` instead.
* `UnregisterHandler(short msgType)`\
  &#x20;Use `NetworkServer.UnregisterHandler()` or `NetworkClient.UnregisterHandler()` instead.
* `Send(int msgType, MessageBase msg, int channelId = Channels.Reliable)`\
  &#x20;Use `Send(msg, channelId)` instead.
* `clientOwnedObjects` renamed to `owned`.

### NetworkServer <a href="#networkserver" id="networkserver"></a>

* `NetworkConnection` was replaced by `NetworkConnectionToClient` in many places.
* `FindLocalObject(uint netId)`\
  &#x20;Use `NetworkServer.spawned[netId].gameObject` instead.
* `RegisterHandler(int msgType, NetworkMessageDelegate handler)`\
  &#x20;Use `RegisterHandler(T msg)` instead.
* `RegisterHandler(MsgType msgType, NetworkMessageDelegate handler)`\
  &#x20;Use `RegisterHandler(T msg)` instead.
*   `RegisterHandler(Action handler, bool requireAuthentication = true)`

    Use `RegisterHandler(Action<NetworkConnection, T), requireAuthentication = true)` instead.
* `UnregisterHandler(int msgType)`\
  &#x20;Use `UnregisterHandler(T msg)` instead.
* `UnregisterHandler(MsgType msgType)`\
  &#x20;Use `UnregisterHandler(T msg)` instead.
* `SendToAll(int msgType, MessageBase msg, int channelId = Channels.Reliable)`\
  &#x20;Use `SendToAll(T msg, int channelId = Channels.Reliable)` instead.
* `SendToClient(int connectionId, int msgType, MessageBase msg)`\
  Use `NetworkConnection.Send(T msg, int channelId = Channels.Reliable)` instead.
* `SendToClient(int connectionId, T msg)`\
  Use `NetworkConnection.Send(T msg, int channelId = Channels.Reliable)` instead.
* `SendToClientOfPlayer(NetworkIdentity identity, int msgType, MessageBase msg)`\
  Use `identity.connectionToClient.Send<T>(T message, int channelId = Channels.Reliable)` instead.
* `SendToReady(NetworkIdentity identity, short msgType, MessageBase msg, int channelId = Channels.Reliable)`\
  &#x20;Use `identity.connectionToClient.Send()` instead.
*   `SendToReady(NetworkIdentity identity, T message, bool includeOwner = true, int channelId = Channels.Reliable)`

    Renamed to `SendToReadyObservers`.
* `SpawnWithClientAuthority(GameObject obj, GameObject player)`\
  &#x20;Use `Spawn(GameObject obj, GameObject player)` instead.
* `SpawnWithClientAuthority(GameObject obj, NetworkConnection ownerConnection)`\
  &#x20;Use `Spawn(GameObject obj, NetworkConnection ownerConnection)` instead.
* `SpawnWithClientAuthority(GameObject obj, Guid assetId, NetworkConnection ownerConnection)`\
  &#x20;Use `Spawn(GameObject obj, Guid assetId, NetworkConnection ownerConnection)` instead.
* `disconnectInactiveConnections` and `disconnectInactiveTimeout` were removed.
* `NoConnections` was renamed to `NoExternalConnections`.
*   `DisconnectAllExternalConnections`  / `DisconnectAllConnections`

    Use `DisconnectAll` instead.
* `OnError` renamed to `OnTransportError` for clarity.

### NetworkClient <a href="#networkclient" id="networkclient"></a>

* `NetworkClient singleton`\
  &#x20;Use `NetworkClient` directly. Singleton isn't needed anymore as all functions are static now.\
  &#x20;Example: `NetworkClient.Send(message)` instead of `NetworkClient.singleton.Send(message)`.
* `allClients`\
  &#x20;Use `NetworkClient` directly instead. There is always exactly one client.
* `GetRTT()`\
  &#x20;Use `NetworkTime.rtt` instead.
*   `readyConnection`

    Use `connection` instead.
*   `isLocalClient`

    Use `isHostClient` instead.
*   `DisconnectLocalServer()`

    Use `NetworkClient.Disconnect()` instead.
* `RegisterHandler(int msgType, NetworkMessageDelegate handler)`\
  &#x20;Use `RegisterHandler(T msg)` instead.
* `RegisterHandler(MsgType msgType, NetworkMessageDelegate handler)`\
  &#x20;Use `RegisterHandler(T msg)` instead.
*   `RegisterHandler(Action<NetworkConnection, T> handler, bool requireAuthentication = true)`

    Use `RegisterHandler(Action<T> handler, bool requireAuthentication = true)` instead.
* `UnregisterHandler(int msgType)`\
  &#x20;Use `UnregisterHandler(T msg)` instead.
* `UnregisterHandler(MsgType msgType)`\
  &#x20;Use `UnregisterHandler(T msg)` instead.
*   `Ready(NetworkConnection conn)`

    Use `Ready()` without the `NetworkConnection` parameter instead.
* `Send(short msgType, MessageBase msg)`\
  &#x20;Use `Send(T msg, int channelId = Channels.Reliable)` with no message id instead
* `ShutdownAll()`\
  &#x20;Use `Shutdown()` instead. There is only one client.
* `OnError` renamed to `OnTransportError` for clarity.

### ClientScene <a href="#clientscene" id="clientscene"></a>

* Merged into `NetworkClient`.

### Network Scene Checker

* Replaced by [Scene Interest Management](../interest-management/scene.md).

### Network Proximity Checker

* Replaced by [Spatial Hash](../interest-management/spatial-hashing.md) / [Distance](../interest-management/distance.md) Interest Management.

### Network Match Checker

* Replaced by Network Match and requires [Match Interest Managemen](../interest-management/match.md)t.

### Network Owner Checker

* Replaced by Network Team and requires [Team Interest Management](../interest-management/team.md).

### Network Authenticator

* `NetworkConnection` was replaced by `NetworkConnectionToClient` in many places.
* `OnClientAuthenticate` no longer takes a `NetworkConnection` parameter.\
  Use `NetworkClient.connection` as needed.
* `OnClientAuthenticated` event no longer takes a `NetworkConnection` parameter.\
  Use `NetworkClient.connection` as needed.
*   `NetworkConnection` is no longer used in client message handlers.

    Use `NetworkClient.connection` within your handlers instead.
* `ClientAccept` and `ClientReject` no longer needs a `NetworkConnection` parameter.

### NetworkTime

* `NetworkTime.timeVar` was renamed to `timeVariance`.
* `NetworkTime.timeSd` was renamed to `timeStandardDeviation`.
* `NetworkTime.rttVar` was renamed to `rttVariance`.
* `NetworkTime.rttSd` was renamed to `rttStandardDeviation`.

### Transport

* `activeTransport` renamed to `active`.

### Messages <a href="#messages" id="messages"></a>

Basic messages of simple types were all removed as unnecessary bloat. You can create your own message classes instead.

* `StringMessage`
* `ByteMessage`
* `BytesMessage`
* `IntegerMessage`
* `DoubleMessage`
* `EmptyMessage`

NetworkMessage requires structs in all cases - classes no longer supported

### NetworkReader <a href="#networkreader" id="networkreader"></a>

* `Read(byte[] buffer, int offset, int count)`\
  &#x20;Use `ReadBytes` instead.
* `ReadPackedInt32(int value)` Use `ReadInt32(int value)` instead.
* `ReadPackedUInt32(uint value)` Use `ReadUInt32(uint value)` instead.
* `ReadPackedUInt64(ulong value)` Use `ReadUInt64(ulong value)` instead.
* `ReadBoolean` renamed to `ReadBool`.
* `ReadInt16` renamed to `ReadShort`.
* `ReadInt32` renamed to `ReadInt`.
* `Readint64` renamed to `ReadLong`.
* `ReadSingle` renamed to `ReadFloat`.

### NetworkWriter <a href="#networkwriter" id="networkwriter"></a>

* `Write(bool value)`\
  &#x20;Use `WriteBool` instead.
* `Write(byte value)`\
  &#x20;Use `WriteByte` instead.
* `Write(sbyte value)`\
  &#x20;Use `WriteSByte` instead.
* `Write(short value)`\
  &#x20;Use `WriteShort` instead.
* `Write(ushort value)`\
  &#x20;Use `WriteUShort` instead.
* `Write(int value)`\
  &#x20;Use `WriteInt` instead.
* `Write(uint value)`\
  &#x20;Use `WriteUInt` instead.
* `Write(long value)`\
  &#x20;Use `WriteLong` instead.
* `Write(ulong value)`\
  &#x20;Use `WriteULong` instead.
* `Write(float value)`\
  &#x20;Use `WriteFloat` instead.
* `Write(double value)`\
  &#x20;Use `WriteDouble` instead.
* `Write(decimal value)`\
  &#x20;Use `WriteDecimal` instead.
* `Write(string value)`\
  &#x20;Use `WriteString` instead.
* `Write(char value)`\
  &#x20;Use `WriteChar` instead.
* `Write(Vector2 value)`\
  &#x20;Use `WriteVector2` instead.
* `Write(Vector2Int value)`\
  &#x20;Use `WriteVector2Int` instead.
* `Write(Vector3 value)`\
  &#x20;Use `WriteVector3` instead.
* `Write(Vector3Int value)`\
  &#x20;Use `WriteVector3Int` instead.
* `Write(Vector4 value)`\
  &#x20;Use `WriteVector4` instead.
* `Write(Color value)`\
  &#x20;Use `WriteColor` instead.
* `Write(Color32 value)`\
  &#x20;Use `WriteColor32` instead.
* `Write(Guid value)`\
  &#x20;Use `WriteGuid` instead.
* `Write(Transform value)`\
  &#x20;Use `WriteTransform` instead.
* `Write(Quaternion value)`\
  &#x20;Use `WriteQuaternion` instead.
* `Write(Rect value)`\
  &#x20;Use `WriteRect` instead.
* `Write(Plane value)`\
  &#x20;Use `WritePlane` instead.
* `Write(Ray value)`\
  &#x20;Use `WriteRay` instead.
* `Write(Matrix4x4 value)`\
  &#x20;Use `WriteMatrix4x4` instead.
* `Write(NetworkIdentity value)`\
  &#x20;Use `WriteNetworkIdentity` instead.
* `Write(GameObject value)`\
  &#x20;Use `WriteGameObject` instead.
* `Write(byte[] buffer, int offset, int count)`\
  &#x20;Use `WriteBytes` instead.
* `WritePackedInt32(int value)`\
  &#x20;Use `WriteInt32(int value)` instead
* `WritePackedUInt32(uint value)`\
  &#x20;Use `WriteUInt32(uint value)` instead
* `WritePackedUInt64(ulong value)`\
  &#x20;Use `WriteUInt64(ulong value)` instead

### RemoteCallHelper

* Renamed to `RemoteProcedureCalls`.
* `CmdDelegate` renamed to `RemoteCallDelegate`.
* `MirrorInvokeType` renamed to `RemoteCallType`.

### Transport <a href="#transport" id="transport"></a>

* `GetConnectionInfo(int connectionId, out string address)`\
  &#x20;Use `ServerGetClientAddress(int connectionId)` instead.
* `GetMaxBatchSize` renamed to `GetMaxPacketSize`.
*   `ClientSend(int channelId, ArraySegment segment)`

    Use `ClientSend(segment, channelId)` instead.
*   `ServerSend(int connectionId, int channelId, ArraySegment segment)`

    Use `ServerSend(connectionId, segment, channelId)` instead.

### Telepathy Transport <a href="#telepathytransport" id="telepathytransport"></a>

* `MaxMessageSize`\
  &#x20;Use `MaxMessageSizeFromClient` or `MaxMessageSizeFromServer` instead.

### Fallback Transport

* This has been removed.

### Utils

* `Version` enum removed.
* `DefaultReliable` renamed to `Reliable`.
* `DefaultUnreliable` renamed to `Unreliable`.
