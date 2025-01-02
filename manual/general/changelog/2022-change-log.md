# 2022 Change Log

{% hint style="info" %}
Mirror is published to the [Asset Store](https://assetstore.unity.com/packages/tools/network/mirror-129321) at the start of every month, unless some critical issue causes a delay.
{% endhint %}

{% hint style="info" %}
Mirror uses semantic versioning, and the versions shown here are those that were published to the Asset Store, and occasionally major version bumps happen mid-month between store submissions and are therefore not individually shown here.
{% endhint %}

## [v71.0.0](https://github.com/MirrorNetworking/Mirror/releases/tag/v71.0.0) -- 2022-Dec-31

* Added: `NetworkClient.activeHost` added as static.
* Added: Sync\* collections now support Client-to-Server Sync Direction.
* Added: Network Manager HUD now has a StopClient button for Host mode.
  * This leaves the server running in Server Only mode and removes the host client.
* Fixed: KCP updated to version 1.26.
* Fixed: `NetworkManager.OnStopClient` now checks if client was started.
* Fixed: `NetworkManager.OnClientSceneChanged` now checks for authenticated client before calling `NetworkClient.Ready`.
* Fixed: `NetworkManager.mode` is now set to `offline` before calling OnStopClient to prevent a recursion.
* Fixed: `SimpleWebSocket` logging improved.
* Fixed: `NetworkManager` template now resets singleton in Awake override.
* Fixed: `NetworkDiscovery` template now correctly has the messages defined as structs.
* Fixed: NetworkTransform / NetworkTransformReliable now check for null connectionToClient in UpdateServer.
  * This cures the NRE's when player object is abandoned by scene changes.
* Fixed: `NetworkTransformChild` `AddComponentMenu` attribute reset since it's obsolete.
* Fixed: `NetworkRigidbody2D` now has `HelpURL` attribute.
* Fixed: `NetworkServer` logs now have game object references for easier debugging.
* Fixed: FPS script had wrong namespace.
* Fixed: Network Authenticators now use `NetworkClient.Send` so we don't bypass a null check.
* Fixed: `NetworkClient.owned` is now correctly cleaned up in OnDestroy.
* Fixed: `NetworkIdentity.spawned` is now correctly cleaned up in OnDestroy.
* Fixed: `SpawnObject` was missing a check for identities already spawned.
  * This cleans up the warnings in Additive Scenes implementations.
* Changed: `NetworkServer.localConnection` is now `LocalConnectionToClient` type.
* Changed: NetworkManager's OnStartServer and OnStartHost are now called later in the startup sequence.
* Changed: `NetworkRoomManager` no longer does max connection checks...`NetworkServer` already does that sooner.
* Obsolete: `NetworkServer.localClientActive` renamed to `activeHost`.
* **BREAKING**: `NetworkTransform` and `NetworkTransformReliable` now inherit from a new `NetworkTransformBase`.

## [v70.0.0](https://github.com/MirrorNetworking/Mirror/releases/tag/v70.0.0) -- 2022-Dec-09

* Added: Support for Unity 2019 LTS was restored
  * No promises as how long this will last. Unity no longer supports it, and code debt adds up.
* Fixed: KCP updated to version 1.23.
* Fixed: Examples have been updated to work with this release.
* Fixed: Interest Management now uses `TryGetValue` in several places.
* Fixed: Telepathy no longer calls OnDisconnected twice incorrectly.
* Fixed: Transport handlers are cleared when NetworkClient restarts.
* Fixed: Several errors with Multiplex Transport were fixed.
* Fixed: Weaver now supports inheritance for `NetworkConnectionParameter`.
* Fixed: Client-To-Server SyncVars weren't being sent to clients.
* Fixed: Logging in transports was improved.
* Changed: NetworkManager.OnClientDisconnect is now an empty virtual method.
  * Script Template also updated.
* **Breaking**: `NetworkTransform` Reliable and Unreliable
  * Unreliable version replaces the old `NetworkTransform`.
  * `SyncInterval` is now managed by `NetworkServer` `sendRate` (set in Network Manager).
  * `NetworkTransformChild` removed...use Network Transform and set target to a child.
  * `clientAuthority` obsoleted in favor of `syncDirection` impemented in previous release.

{% hint style="info" %}
Support for Unity 2019 LTS was restored in this release, but we cannot promise how long we'll be able to maintain compatibility as code debt adds up.
{% endhint %}

## [v69.0.0](https://github.com/MirrorNetworking/Mirror/releases/tag/v69.0.0) -- 2022-Nov-10

{% hint style="info" %}
This version was published to the Asset Store as 2022-10.
{% endhint %}

* Added: CCU Test example.
* Added: `NetworkStatistics` component.
* Fixed: `NetworkRigidbody` now uses double time to keep precision over long runtime.
* Fixed: `NetworkLerpRigidbody` now uses double time to keep precision over long runtime.
* Fixed: `NetworkWriter.WriteString()` now ensures capacity before writing.
* Changed: Tools folder created and some rearrangement was done.

## [v68.0.0](https://github.com/MirrorNetworking/Mirror/releases/tag/v68.0.0) -- 2022-Oct-21

{% hint style="info" %}
This version was published to the Asset Store as 2022-09.
{% endhint %}

* Added: Snapshot Interpolation example.
* Added: `NetworkPingDisplay` now has configurable size.
* Added: `NetworkManager.GetStartPosition` is now virtual so it can be overridden.
* Added: Network Manager now has an Auto Connect option for headless clients / CCU testing.
* Added: `NetworkConnection` now maintains `owned` HashSet on both server and client.
* Added: `NetworkBehaviour` now has `SyncDirection` to make client authority components easier.
  * Note: This feature will continue to evolve and may change in future releases.
* Fixed: Benchmark, Chat and Tanks examples were improved.
* Fixed: Network Manager script template was updated.
* Fixed: `NetworkBehaviour` logging was improved.
* Fixed: `SimpleWebTransport` header lookup is now case-insensitive.
* Fixed: Spatial Hash Interest Mgmt. now calculates visibility range correctly.
* Fixed: Read/Write GUID no longer allocates garbage.
* Fixed: Telepathy `ClientDisconnect` now calls `Disconnect` event correctly.
* Fixed `NetworkServer.Spawn` now checks and warns for duplicates.
* Fixed: `NetworkIdentity` now only checks `Application.isPlaying` in Editor context.
* Fixed: Network Statistics properties made public so other components can access them.
* Fixed: `InterestManagement.Awake` is now `protected virtual` so it can be overridden.
* Fixed: `NetworkIdentity` serialization of `componentIndex` reduced to \~1 bit instead of 1 byte.
* Fixed: `NetworkBehaviour` serialization of headers reduced to 1 byte instead of 4 bytes.
* Fixed: `NetworkClient` now resets time interpolation whenever host connects or reconnects.
* **Changed**: `NetworkBehaviour.ComponentIndex` reduced from `int` to `byte`.
* **Changed**: Snapshot Interpolation refactored to version 2.
* **Changed**: Latency Simulator `latencySpike` renamed to `jitter`.
* **Changed**: Snapshot Interpolation split into time + value interpolation.
* **Breaking**: Transports folder was moved up and various transports moved into that folder.
* **Breaking**: Runtime folder renamed to Core.
* **Breaking**: `NetworkIdentity` / `NetworkBehaviour` `OnSerialize` no longer returns a `bool`.
* **Breaking**: `NetworkBehaviour.SerializeSyncVars` no longer returns a `bool`.
* **Breaking**: `assetId` in `SpawnMessage` changed from `Guid` to `uint`.
* **Breaking**: `OnError(Exception)` is now `OnError(TransportError, string)` where `TransportError` is an enum and string is free text provided by transports.
* **Breaking**: `NTSnapshot` renamed to `TransformSnapshot` for clarity.
* **Breaking**: `NetworkTime` was rewritten to use `NetworkClient`'s snapshot interpolation timeline.
* **Obsolete**: `NetworkManager.serverTickRate` renamed to `sendRate`.
* **Obsolete**: `NetworkManager.serverTickinterval` was moved to `NetworkServer`.
* **Obsolete**: `NetworkTransport.activeTransport` renamed to `active`.
* **Obsolete**: `NetworkBehavior.hasAuthority` renamed to **isOwned** for clarity.
* **Obsolete**: `NetworkConnection.clientOwnedObjects` renamed to `owned` for clarity.

## [v67.1.0](https://github.com/MirrorNetworking/Mirror/releases/tag/v67.1.0) -- 2022-May-05

* Fixed: KCP updated to version 1.19.
* Fixed: Team Interest Mgmt `OnDestroyed` logic was improved.
* Fixed: Comments in various Network Authenticators were updated.
* Fixed: Benchmark example was improved so movement revolves around origin.
* Fixed: Cmd/Rpc bandwidth reduced from 4 bytes to 2.
* Fixed: `Texture2D` now includes dimensions and reads `Color32` correctly and supports nulls.
* Fixed: `NetworkServer.SendToObservers` now correctly uses `NetworkConnectionToClient`.
* Fixed: `NetworkClient` now checks and warns for duplicate scene ID.
* **Changed**: `NetworkClient` / `NetworkServer` `OnError` renamed to `OnTransportError` for clarity.
* **Breaking**: Obsoletes were removed.

## [v66.0.9](https://github.com/MirrorNetworking/Mirror/releases/tag/v66.0.9) -- 2022-Apr-17

{% hint style="warning" %}
**NOTE:** This was to be the last Mirror release to support Unity 2019 LTS, as it has reached end of life by Unity.  Support was restored in Mirror 70.0.0 above.
{% endhint %}

* Added: [Network Statistics](../../components/network-statistics.md) component, showing messages & packets sent & received per second.
* Added: `Queue.TryDequeue` extension added for Unity 2019 & 2020 compatibility.
* Fixed: `NetworkClient.ChangeOwner` now uses `isLocalPlayer` flag to check if `OnStopLocalPlayer` should be called.
* Fixed: `ReadNetworkBehavior` now correctly reads data on client even if the associate `NetworkIdentity` is missing from the client.
* Fixed: `NetworkClient.SpawnPrefab` now correctly looks for custom spawn handlers before using registered prefabs list instead of after.
* Fixed: `NetworkWriterExtensions` now uses `WriteArray` instead of `Write`.
* Fixed: Attempting to serialize prefabs and not-spawned game objects throws a more obvious exception.
* Fixed: `DestroyAllClientObjects` now also resets after unspawn handler called.
* Fixed: Calling `StopClient` in Host mode no longer resets `nextNetId`.
* Fixed: Calling `StopClient` in Host mode no longer destroys objects on clients.
* Fixed: It should now be possible to call StopClient in Host mode to leave the server in a running "Server Only" state, keeping all clients connected and running normally.
* Fixed: `AggressiveInlining` attribute added in many places to improve performance.
* **Changed:** `NetworkWriter` / `NetworkReader` API simplified.
* **Changed:** `PooledNetworkReader` / `PooledNetworkWriter` renamed to `NetworkReaderPooled` / `NetworkWriterPooled`.
* **Changed:** Transport base class moved to Runtime folder, Transport folder renamed to Transports

## [v65.0.0](https://github.com/MirrorNetworking/Mirror/releases/tag/v65.0.0) -- 2022-Mar-09

* Added: Command / ClientRpc / TargetRpc can now be overloaded.
* Added: Network Behaviour now has `OnStopLocalPlayer` virtual method, invoked right before `OnStopClient`. Script Template has also been updated.
* Added: Network Manager Template overrides `singleton` to eliminate the need to cast Network Manager's singleton to derived class type.
* Added: Support for generic `NetworkBehaviour<T>` subclasses.
* Fixed: Network Reader/Writer uses blittable reads/writes where possible.
* Fixed: Android compatibility for Read/Write blittable.
* Fixed: Android Multicast Discovery.
* Fixed: `NetworkTransformChild` now correctly syncs initial state when spawned.
* Fixed: Latency Simulation now uses `Time.unscaledTime`.
* Fixed: `SimpleWebsocketTransport` has been updated to fix a number of bugs, including compatibility with Unity 2021.x, and handling of messages larger than 64K.
* **Changed**: A bunch of virtual methods now take `NetworkConnectionToClient` instead of `NetworkConnection`.
* **Changed**: `RemoteCallHelper` renamed to `RemoteProcedureCalls`.
* **Changed**: `CmdDelegate` renamed to `RemoteCallDelegate`.
* **Changed**: `MirrorInvokeType` renamed to `RemoteCallType`.
* **Changed:** `NetworkManager.clientLoadedScene` is now `protected`.
* **Obsolete**: `NetworkTransform` now has overloads for `CmdTeleport` / `RpcTeleport`  with Quaternion rotation, deprecating separate `CmdTeleportAndRotate` / `RpcTeleportAndRotate` methods, made possible by implementing Command / ClientRpc overloads mentioned above.
* **Obsolete:** Experimental Network Transform components.  Use the regular ones instead.

## [v57.0.0](https://github.com/MirrorNetworking/Mirror/releases/tag/v57.0.0) -- 2022-Jan-12

* Fixed: KCP updated to version 1.17.
* Fixed: Use Read/Write blittable for performance improvement.
* **Removed**: Obsolete Network Visibility.

## [v55.3.8](https://github.com/MirrorNetworking/Mirror/releases/tag/v55.3.8) -- 2022-Jan-05

* Added: [Team Interest Management](../../interest-management/team.md) and Network Team components.
* Added: [Device Authenticator](../../components/network-authenticators/device-authenticator.md).
* Added: Custom Interest Management [Script Template](../script-templates.md).
* Added: Custom Network Transform [Script Template](../script-templates.md).
* Added: Rotation parameter added to `NetworkTransform` `CmdTeleport` / `RpcTeleport`.
* Added: Support for a bunch more nullable types.
* Added: [Additive Levels](../../examples/additive-levels.md) example.
* Added: Texture2D and Sprite are now supported [Data Types](../../guides/data-types.md).
* Fixed: Fast Enter Play mode without Reload Domain now supported by resetting all statics.
* Fixed: Network Transform now uses less bandwidth by only syncing changes based on sensitivity.
* Fixed: KCP updated to version 1.15.
* Fixed: `ReplacePlayerForConnection` now correctly updates `isLocalPlayer` flag.
* Fixed: `OnStopClient` no longer called twice in certain cases.
* Fixed: `NetworkServer` now checks for `active` before invoking Interest Management updates.
* Fixed: Objects spawned with a client owner no longer lose ownership when respawned via interest management.
* Fixed: Delayed Disconnect in Basic Authenticator now uses `HashSet` of connections pending disconnect.
* Fixed: `NetworkTransform` now checks for ready client before sending updates to server.
* Fixed: `OnStartLocalPlayer` now correctly called again after `RemovePlayerForConnection` followed by `AddPlayerForConnection` with the same player object.
* Changed: `NetworkMatch` moved to `InterestManagement/Match` folder.
* Changed: Internal code for `SyncVar`. Resetting non-default inspector values may be required.
* Changed: Tanks example updated.
* Changed: Chat example updated...overhauled really.
* Changed: Network Reader / Writer now use blittable serialization where possible (4-6x performance improvement).
* **Breaking**: Removed obsolete `ConfigureServerFrameRate`\
  Use `ConfigureHeadlessFrameRate` instead.
* **Breaking**: Removed obsolete `PersistNetworkManagerToOfflineScene`.
* **Breaking**: `NetworkAuthenticator` `OnClientAuthenticated` event no longer needs a NetworkConnection parameter.
* **Obsolete**: OnClient\* virtual methods in Network Manager no longer take a `NetworkConnection` parameter.
* **Obsolete**: Network Room Manager client overrides no longer have `NetworkConnection` parameter.  Use `NetworkClient.connection` in your overrides instead.
