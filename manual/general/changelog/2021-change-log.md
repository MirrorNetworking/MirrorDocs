# 2021 Change Log

{% hint style="info" %}
Mirror uses semantic versioning, and the versions shown here are those that were published to the Asset Store, and occasionally major version bumps happen mid-month between store submissions and are therefore not individually shown here.
{% endhint %}

## [v53.0.0](https://github.com/MirrorNetworking/Mirror/releases/tag/v53.0.0) -- 2021-Oct-20

* Added: `ChangeOwnerMessage`. `NetworkIdentity.AssignClientAuthority` and `RemoveClientAuthority` now send this message instead of a `SpawnMessage`, so transform data is no longer reset to server values, so no more snap-back of moving objects.
* Added: `SyncDictionary` can now be declared and initialized from an existing List.
* Added: Nullable color added to Data Types Mirror handles.
* Added: `ILPostProcessor` is now used for Unity versions 2020.3 and later.
* Fixed: Stopping Discovery no longer causes a crash.
* Fixed: Additive Scenes example had misplaced scene objects.
* Fixed: `NetworkManager.ServerChangeScene` now aborts if scene change already in progress.
* Fixed: `hasAuthority` is now properly cleared on client when unspawning scene objects.
* Fixed: Match Interest Management now correctly handles networked objects without `NetworkMatch` components, and no longer throws Key not found exception in certain cases.
* Fixed: `NetworkManager.StopClient` no longer fires twice in certain cases.
* Fixed: `NetworkMatch` was missing attributes.
* Changed: `NetworkLoop` static class is now public.
*   Changed: `NetworkConnection.observing` is now public.

    Changed: `NetworkTransformV2` default values updated. `interpolateScale` also defaults to `false` now.
* Changed: `NetworkTransformV2` `isClientAuthority` now `protected` instead of `private`.
* Changed: Examples using `NetworkTransformV2` now using default values.
* Changed: Updated `NetworkManager` Script Template.
* Changed: Tanks example has health bars now.
* Changed: `componentIndex` is now a `byte` in `CmdMessage` and `RpcMessage` structs.
* Changed: `SyncObject` is now a class instead of an interface.
* **Obsolete**: `SyncObject.Flush` - Use `ClearChanges` instead.
* **Obsolete**: `NetworkBehaviour.getSyncVarHookGuard` - Use `GetSyncVarHookGuard` instead.
* **Obsolete**: `NetworkBehaviour.setSyncVarHookGuard` - Use `SetSyncVarHookGuard` instead.
* **Obsolete**: `NetworkBehaviour.SetDirtyBit` - Use `SetSyncVarDirtyBit` instead.
* **Obsolete**: `NetworkServer.SendToReady` - Use `SendToReadyObservers` instead.
* **Removed**: Various obsolete methods that were at least 3 months old.
* **Removed**: Fallback transport -- no longer included or supported.

## [v46.0.4](https://github.com/MirrorNetworking/Mirror/releases/tag/v46.0.4) - 2021-Sep-03

* Added: Tanks example now has `GUIConsole` for easier debugging.
* Fixed: `UnpackAndInvoke` now throws error and disconnects in case of missing handlers.
* Fixed: `NetworkServer`/`NetworkClient` now detects ever-growing batches.
* Fixed: Weaver now uses `ILPostProcessing` in Unity 2020+.
* Fixed: Movement delay in `NetworkTransformV2`.
* Fixed: `NetworkTransformBase.OnDrawGizmos` now only runs in play mode.
* Fixed: Added missing `using` to Network Manager template.
* Fixed: Some `OnGUI` calls are now wrapped in `#if UNITY_EDITOR || DEVELOPMENT_BUILD`.
* Changed: `NetworkEarlyUpdate`/`NetworkLateUpdate` in `NetworkLoop` now exposed via Action events.
* Changed: Multiple Matches example now uses Match Interest Management.
* Changed: Multiple Additive Scenes example now uses Scene Interest Management.
* **Obsolete**: `NetworkIdentity.spawned` has been split into `NetworkServer.spawned` and `NetworkClient.spawned`.
* **Removed**: Network Observer template.

## [v44.0.2](https://github.com/MirrorNetworking/Mirror/releases/tag/v44.0.2) -- 2021-Aug-08

* Added: Scene Interest Management.  Put this on Network Manager and remove all Network Scene Checker components.
* Added: Match Interest Management.  Put this on Network Manager and replace Network Match Checker with new Network Match component.
* Added: Interest Management now has `OnSpawned` / `OnDestroyed` events.
* Added: Network Transform V2 with Snapshot Interpolation.
* Added: Network Transport exception events that transports can raise to Network Manager's `OnServerError` and `OnClientError` overrides.  Transports still need to implement their part.
* Added: `ZigZag VarInt` added back to Compression module.
* Fixed: Network Discovery HUD now calls `StopDiscovery` when stopping server/host/client.
* Fixed: `NetworkTime.lastPingTime` is now reset properly.
* Fixed: KCP updated to version 1.12.
* Fixed: `NetworkBehaviour` dirty check uses double time.
* Fixed: `NetworkTime.UpdateClient` uses double precision time for accuracy over days/weeks.
* Fixed: `NetworkAnimator` uses double precision time for accuracy over days/weeks
* Fixed: Quaternion Compression would produce wrong result for certain values.
* Fixed: Added missing `[Command]` attribute to `NetworkAnimator.CmdSetAnimatorSpeed`.
* Fixed: `DestroyObject` is now called when cleaning up scene identities too. Sends unspawn messages and calls `OnStopClient`/`OnStopServer`properly.
* Fixed: Clear `hasAuthority` when destroying and call `OnStopAuthority`.
* Changed: Distance Interest Management now has custom range component, replacing Network Proximity Checker.
* Changed: Per-scene interest management replaces Network Scene Checker.
* Changed: Network Match component separated from Network Match Checker.
* Changed: Match Interest Management replaces Network Match Checker.
* Changed: `NetworkManager.isNetworkActive` is now a readonly bool.
* Changed: `NetworkManager.ConfigureServerFrameRate` renamed to `ConfigureHeadlessFrameRate`.

## [v42.2.12](https://github.com/MirrorNetworking/Mirror/releases/tag/v42.2.12) -- 2021-Jul-12

* Added: Timestamp-based batching. Batching is always on now and is bidirectional.
* Added: `NetworkServer` / `NetworkClient` `OnConnected` / `OnDisconnected` events now public so custom Network Managers can hook into them.
* Fixed: `NetworkServer` / `NetworkClient` `Shutdown` now correctly clears `OnConnected` / `OnDisconnected` event handlers.
* Fixed: Scene loading with batching works now.
* Fixed: KCP 1.11 significantly reduced garbage allocations.
* Fixed: Fallback Transport didn't propagate Early/Late Update.
* Fixed: `OnClientDisconnect` wasn't being called for host client.
* Fixed: Interest Management V2 now allows `SetHostVisibility` overwriting.
* Fixed: Serialization precision over days using frame count instead of single precision time.
* Fixed: Added null check and error logging when `networkBehaviours` array is null.
* Fixed: Force calling `NetworkIdentity.Awake` when parent is inactive.
* Fixed: `OnClientChangeScene` was not firing for host client.
* Fixed: Avoid NRE in `UnpackAndInvoke`.
* Fixed: Null handling in `WriteUri` and `ReadUri`.
* Fixed: `FinishLoadScene` should not be called when `customHandling` is true (Additive Scenes).
* Fixed: Prevent assetId from being set to an empty string.
* Fixed: `clientAuthority` is now public in `NetworkRigidbody` / `NetworkRigidbody2D`.
* Changed: Transports are no longer paused (disabled) during scene loading. Message processor handles this correctly now, so transports can be simplified.

## [v40.0.9](https://github.com/MirrorNetworking/Mirror/releases/tag/v40.0.9) -- 2021-Jun-08

* Added: `ServerAuthFailed` bool to Basic Authenticator.
* Added: Discovery HUD now has a Stop Button.
* Fixed: KCP updated to version 1.10.
* Fixed: Telepathy updated to version 1.8.
* Fixed: `NetworkIdentity` default script execution order set to -1.
* Fixed: Discovery now properly shuts down in `OnDisable` / `OnDestroy`.
* Fixed: Discovery now stops discovering when client is connected to game server.
* Fixed: `NetworkTransform` sync scale and interpolation.
* Fixed: Player object now prevented from being added to Network Manager's spawnable prefabs list
* Fixed: Simple Web Sockets transport SSL Handshake.
* Fixed: `NetworkServer.OnDisconnect` now calls `DestroyPlayerForConnection` if `NetworkManager` doesn't handle it.
* Fixed: `NetworkClient.Disconnect` now checks for null connection and that it's not called twice.
* Fixed: `NetworkTime` now resets when server starts.
* Fixed: Basic example `PlayerUI` text corrected.
* Fixed: Timeouts in Telepathy fixed.
* Fixed: Observers are now correctly cleared after other clients disconnect.
* Changed: `NetworkConnection` parameter removed from Network Authenticator's Client messages.
* Changed: `MessagePacking GetId<T>` return value changed to `ushort`.
* Changed: `Transport.ServerDisconnect` return is now void instead of bool.
* Removed: Network Manager's Idle Timeout.
* Removed: Cloud API and examples.
* **Obsolete**: Fallback Transport will be removed in a later release.

## [v35.1.0](https://github.com/MirrorNetworking/Mirror/releases/tag/v35.1.0) -- 2021-Mar-29

* Added: Network Discovery now has a bool to disable automatic active discovery.
* Added: `PersistNetworkManagerToOfflineScene` to Network Manager...see tooltip.
* Added: Latency Simulation Transport that can be added to Network Manager and have normal transport chained to it to simulate network latency.
* Added: Sync Mode tooltip.
* Added: Headless clients now set `targetFramerate` like headless servers
* Added: `NetworkTransform` now has the following features:
  * Compress Rotation option
  * Sync Scale option
  * Interpolate Scale option
* Fixed: No Writer found / No Reader found errors have been resolved.
  * **NOTE:** You may need to delete your `Library/ScriptAssemblies` folder and IL2CPP cache
* Fixed: `NetworkIdentity.isLocalPlayer` is only set, but never reset. Fixes a bug where `isLocalPlayer` would be false in `OnDestroy`, so some components couldn't rely on it in `OnDestroy`.
* Fixed: `NetworkIdentity.Reset` now resets `isLocalPlayer` _after_ the `isLocalPlayer` /`ClearLocalPlayer` check.
* Fixed: `NetworkIdentity.OnDestroy` now only clears `NetworkClient.localPlayer` if we are still the local player.
* Fixed: Client Shutdown is no longer run twice via user code calling `StopClient`.
* Fixed: `SyncVar` field label not shown in Unity 2020 versions (Unity bug).
* Changed: Network Manager `OnServerAddPlayer` now adds the connection id to the object name (on the server only, not synced to clients).
* Changed: Several fields in `NetworkTime` were renamed.  The previous field names are still there and **obsolete** and will be removed in a later version.
* Changed: Most of `ClientScene` is now **obsolete**, use same or similar properties in `NetworkClient` instead.  `ClientScene` will be fully deprecated and removed in a later version.
* Changed: `NetworkClient.RegisterHandler` overload that included a `NetworkConnection` is now **obsolete** and will be removed in a later version.  Handlers can use `NetworkClient.connection` directly.
* Changed: Channel constants names were shortened.  The original constants are still there and **obsolete**, and will be removed in a later version.
* **Breaking**: Minimum supported Unity version is now 2019.4.x LTS.
* **Breaking**: `ClientScene.onLocalPlayerChanged` removed.
* **Breaking**: Compiler symbols culled to Mirror 17 and later versions.

## [v32.1.4](https://github.com/MirrorNetworking/Mirror/releases/tag/v32.1.4) -- 2021-Mar-08

* Added: `NetworkIdentity` now has `isServer` / `isClientOnly`.
* Added: Global Interest Management. This may eventually lead to replacement of Scene Checker, Match Checker, and Owner Checker components.
* Added: GUI Console ported from uMMORPG (F12 to show/hide).
* Added: Network Manager now takes itself out of DDOL in `StopServer` / `StopClient` when offline scene is defined to avoid singleton collision.
* Added: Message batching.  Enable in Network Manager.
* Added: Custom Player Loop:
  * `NetworkEarlyUpdate` (before any Update/FixedUpdate)
  * `NetworkLateUpdate` (after any Update/FixedUpdate/LateUpdate) loops
* Added: KCP Tick split into `TickIncoming` / `TickOutgoing` to utilize the new NetworkEarly/LateUpdate functions...minimizes latency.
* Added: Push > Pull Broadcasting Part 1.
* Fixed: Telepathy 1.7 receive timeout disabled by default, increased `MirrorTransport` timeout to 30sec from 5sec to cover scene changes.
* Fixed: `NetworkServer.SpawnObserversForConnection` refactored to support all visibility cases (`ForceShown` / `ForceHidden` / `Default`) and all systems (legacy / new / default).
* Fixed: `NetworkManager.StopServer/StopClient`: avoid `NullReferenceException` when called in `OnApplicationQuit` or from tests.
* Fixed: `NetworkServer.Update`: inactive connection check moved into the main connections `foreach` loop. Eliminates a `foreach`-connections loop.
* Fixed: `NetworkServer.Update`: separate connections update loop moved into the main loop. Now there is only one connections loop.
* Changed: `NetworkManagerHUD.showGUI` is now obsolete and will be removed in a later version.
* Changed: `SendToClientOfPlayer` is now obsolete and will be removed in a later version.
* **Breaking**: `LogFactory` and logging asset feature removed.
* **Breaking**: Command `ignoreAuthority` parameter renamed to `requiresAuthority`, default true.
* **Breaking**: `ClientRpc` `excludeOwner` parameter renamed to `includeOwner`, default true.
* **Breaking**: `ClientScene.spawnableObjects` is no longer public.

## [v30.5.3](https://github.com/MirrorNetworking/Mirror/releases/tag/v30.5.3) -- 2021-Feb-15 <a href="#version-3053----2021-feb-15" id="version-3053----2021-feb-15"></a>

* Fixed: Fixed a bug in batching for messages larger than MTU.
* Fixed: Telepathy 1.6 fixes data races and improves stability.
* Fixed: KCP 1.8 fixes empty message sending / receiving undefined behaviour and fixes IPv6 on Nintendo Switch.
* Changed: Network Manager default max connections is now 100.
* **Breaking**: `NetworkServer` / `NetworkClient` don't use Connect/Disconnect messages any more.  Connect/Disconnect events are raised by the transport instead.
* **Breaking**: `OnServerError` / `OnClientError` removed since no transport ever implemented them.
* **Breaking** Removed `NetworkConnection.InvokeHandler` as no longer needed.

## [v30.5.0](https://github.com/MirrorNetworking/Mirror/releases/tag/v30.5.0) -- 2021-Feb-05 <a href="#version-3050----2021-feb-05" id="version-3050----2021-feb-05"></a>

* Added: [KCP Transport](https://mirror-networking.com/docs/Articles/Transports/KCPTransport.html) updated to version 1.7.
* Added: Batching support for server messages - See Network Manager to enable.
* Added: [Network Owner Checker](https://mirror-networking.com/docs/Articles/Components/NetworkOwnerChecker.html).
* Added: [Script Templates](https://mirror-networking.com/docs/Articles/General/ScriptTemplates.html) are back in the package -- See the new Mirror section in the Assets > Create menu.
* Added: Unreliable channel added to KCP Transport.
* Fixed: Performance improvements to KCP Transport.
* Fixed: Examples with UI now work with Unity 2018.4 again
* Fixed: `NetworkServer.OnConnected` allows for `connectionId` < 0 now.
* Fixed: Corrected `NetworkRoomPlayer` `ReadyStateChanged` `oldReadyState` parameter name.
* Changed: Ping/Pong messages now use unreliable channel if available from transports.
* **Breaking** Read / Write Blittable has been removed.
