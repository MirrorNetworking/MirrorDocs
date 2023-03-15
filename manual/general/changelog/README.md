# Change Log

{% hint style="info" %}
Mirror is published to the [Asset Store](https://assetstore.unity.com/packages/tools/network/mirror-129321) at the start of every month, unless some critical issue causes a delay.
{% endhint %}

{% hint style="info" %}
Mirror uses semantic versioning, and the versions shown here are those that were published to the Asset Store, and occasionally major version bumps happen mid-month between store submissions and are therefore not individually shown here.
{% endhint %}

## v79.0.0 -- In Progress

* Added: Writer / Reader support for `DateTime` data type.
* Added: `NetworkRoomManager` - `ReadyStatusChange` is now a virtual method.
* Added: `NetworkTransform` interpolation can now be disabled for pos / rot / scale.
* Added: `ExponentialMovingAverage` now has a `Reset` method.
* Added: `NetworkClient`'s Snapshot Interpolation settings exposed in Network Manager.
* Fixed: `NetworkBehaviour` SyncVar uses `netId` lookup.
* Fixed: No longer use custom `NetworkBehaviour` writers for SyncVars.
* Fixed: Writer for non-spawned `NetworkBehaviour` only writes 0 for `netId`.
* Fixed: KCP updated to version 1.33.
* Fixed: Simple Web Transport improved logging.
* Fixed: `NetworkTime` - `PingWindowSize` shortened to 6 from 10 and use it in `ExponentialMovingAverage`.
* Fixed: NetworkLoop no longer adds functions twice when Domain Reload is disabled.
* Fixed: NetworkLoop no longer runs in edit mode.
* Fixed: Network Messages now uses a static NetworkMessageId class and static ushort Id.
* Fixed: Weaver now weaves in pre-computed function hash for RPCs.
* Fixed: Snapshot Interpolation received various updates.
* Fixed: NetworkTransform (Reliable) received various performance updates, fixed jitter.
* Changed: Interest Management now derives from an abstract base class.
* Changed: NetworkWriter - MaxStringLength changed to ushort.MaxValue.
* **Breaking Changes:**
  * Removed obsolete OnServerError / OnClientError.
  * Removed old Unity 2018 / 2019\_3 compiler defines.
  * Remove NetworkClient.serverIp.
  * AsmDef files updated and simplified.

## v73.0.0 -- 2023-Feb-09

* Added: `NetworkClient.OnGUI` now includes BTM for `bufferTimeMultiplier`.
* Fixed: Client objects are now cleared before spawn handlers.
* Fixed: `NetworkIdentity` doesn't reset `SyncObjects` anymore.
  * You can call the Reset method yourself on Sync\* collections.
* Fixed: InitSyncObject IsWritable/IsRecording permissions fixed for host mode and other player's objects on client.
* Fixed: Sync\* collections can now be modified before spawning again.
* Fixed: Client Sync\* collections can be modified when offline now.
* Fixed: Handlers are no longer double firing for Sync\* collections.
* Fixed: SyncList handler for OP\_SET index parameter fixed.
* Fixed: `SpawnObjects` now activates but does not spawn objects under inactive parents.
* Fixed: `NetworkTransformBase` now checks for missing Main Camera in OnGUI.
* Fixed: `NetworkTransformBase` now resets when changing authority in Client To Server mode.
* Fixed: `NetworkTransformBase` OnClientAuthorityChanged is Server Only.
* Fixed: `NetworkTransformReliable` now correctly uses `localScale` for syncing scale.
* Fixed: `NetworkTransformReliable` now checks for changes after previous changes applied.
* Fixed: Jitter from `NetworkTransformReliable` sending last known snapshot fixed.
* Fixed: `NetworkServer.DisconnectAll` doesn't set active to false anymore...it just disconnects all connections.
  * `Shutdown` still properly cleans up all objects and sets active to false.
* Fixed: Reset of statics in `NetworkClient` and `NetworkServer`.
* Fixed: `ChangeOwner` message handler now adds / removes from client connection's `owned` HashSet.
* Fixed: `NetworkClient` now sets `NetworkIdentity.connectionToServer` before `OnStartAuthority` / `OnStartClient` are called.
* Fixed: `isClient` and `isOwned` are now properly set in `OnStartAuthority`.
* Fixed: `isClient` and `isOwned` are now properly set when Interest Management is involved.
* Fixed: KCP updated to version 1.29.
* Fixed: OnClientDisconnect is now correctly invoked for DNS resolution failures.
* Fixed: Removed invalid reference from KCP Assembly Definition file.
* Fixed: Added SimpleWebTransport to Mirror.Transports Assembly Definition file.
* Fixed: Most examples were reviewed and updated, improved PlayerController.
* Fixed: Distance Interest Management now caches references to custom distance components.
* Changed: `OnClientConnect` now fires as soon as authenticated, before any scene change.
* Changed: Use GUID's enabled for all Assembly Definition files.
* Changed: Network Manager HUD layout is now a bit wider for less wrapping.
* Changed: OnGUI in Network Client is now public.
* Changed: Compiler symbols have been culled to those covering the last 12 months and will continue to be culled to a rolling 12-month time period.
* Changed: Latency Simulation Transport configuration has been revamped.
* Breaking Change: Older obsoleted properties and methods have been removed.
