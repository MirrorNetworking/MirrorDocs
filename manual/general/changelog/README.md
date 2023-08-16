# Change Log

{% hint style="info" %}
Mirror is published to the [Asset Store](https://assetstore.unity.com/packages/tools/network/mirror-129321) at the start of every month, unless some critical issue causes a delay.
{% endhint %}

{% hint style="info" %}
Mirror uses semantic versioning, and the versions shown here are those that were published to the Asset Store, and occasionally major version bumps happen mid-month between store submissions and are therefore not individually shown here.
{% endhint %}

{% hint style="info" %}
For further details on versions and their specific change logs, see github release page:\
[https://github.com/MirrorNetworking/Mirror/releases](https://github.com/MirrorNetworking/Mirror/releases)
{% endhint %}

## v81.4.0 -- 2023-Aug-03

* Examples: added BenchmarkIdle demo to test with 80% idle objects
* feature: NetworkTransform WorldScale option
* NetworkClient: improve 'did not find target for sync message' warning
* fix: SimpleWebTransport conditionals removed so users can see error m…
* kcp2k V1.37 \[2023-07-31]
* Unity 2021.3.29 LTS for latest fixes
* feature: HistoryBounds MVP (#3563)
* Tanks co-op example, take control of vehicles via Mirrors Authority a…
* fix: NetworkReader ReadBytes/Array/List allocation limit to pre…
* remove unused import
* NetworkWriter: clean up old UIntDouble comments
* NetworkReader/Writer Read/WriteList: comments
* fix: NetworkConnection "cannot send packet larger than ..." message n…
* fix: NetworkMessages.MaxContentSize now considers channelId instead o…
* feature: bring back NetworkServer.disconnectInactiveConnections (#3556)
* fix: remove redundant RPC buffering. saved bandwidth, but introduced …
* fix: NetworkIdentity.AssignAssetId() is now saved properly. fixes a b…
* fix: NetworkTime RTT now checks for malicious timestamps from the future
* Compression: remove unnecessary QuaternionElement function
* Compression: more obvious TenBitsMax representation
* feature: NetworkConnectionToClient.rtt via Ping & Pong Messages (#3545)
* Examples: default send rate increased from 30 Hz to 60 Hz for remaini…
* perf: default send rate increased from 30 Hz to 60 Hz for users to ge…
* Tanks demo: increase send rate from 30 Hz to 120 Hz to avoid rtt bein…
* NetworkBehaviour.OnValidate: GetComponentInParent(bool) 2020 support …
* NetworkIdentity: correct GetComponentInParent explanations (credit: F…
* NetworkIdentity: expose AssetGuidToUint in builds
* fix: NetworkBehaviour.OnValidate can't find parent NetworkIdentity be…
* fix: Tanks demo: replace Turret obsolete NetworkTransform with Networ…
* perf: remove some unnecessary inlining to optimize instruction cache
* NetworkIdentity: expose AssetId to Guid translation function
* fix: 2019 tests work again (#3547)
* fix: NetworkBehaviour OnValidate #ifdef adjusted for Unity 2020.3.19 …
* improve error message for #3525 for cases where the \[SyncVar] is name…
* fix NetworkIdentityTests by applying the workaround for #3525
* fix: #3525 by explaining the solution in the Error message.
* NetworkClient OnGUI: better rtt display
* breaking: NetworkTime.PingFrequency renamed to PingInterval. Makes it…
* fix PrepareToSpawnSceneObjects tests after recent fix
* NetworkTime: more readable ping interval check
* fix: #3538 calling unspawn on scene objects in DestroyOwnedObjects (#…
* fix: PrepareToSpawnSceneObjects checks netId instead of activeSelf \[f…
* feature: Lag Compensation V1 (#3534)
* Tests: fix failing NetworkManagerTests because DeeplyTested test didn…
* NetworkTime: rttVar renamed to rttVariance for consistency with old M…
* fix: SnapshotInterpolation.Insert() now has a bufferLimit to avoid ev…
* perf: NetworkTransformBase SortedLists now allocate with a default ca…
* Readme updated: clarify incentives, fix typos, improve formatting
* feature: ConnectionQuality Heuristic + Callback + GUI (#3526)
* NetworkTime: reintroduce Round Trip Time Variance to prepare for Conn…
* NetworkClient: initialBufferTime for debugging, comparisons, and to p…
* Tanks demo: always show time interpolation GUI for easier debugging
* fix: NetworkClient TimeInterpolation: add 'current' bufferTimeMultipl…
* Disable the failing tests for now, so at least the project compiles w…
* fix: Weaver SyncVarAttributeAccessReplacer now detects modifying \[Syn…
* Weaver: SyncVarAccessReplacer: pass Logging
* Weaver: SyncVarAccessReplacer: syntax & comments to make it more obvious
* fix: Weaver now weaves NetworkBehavious in nested type definitions
* feature: Weaver adds 'bool Weaved()' to each NetworkBehaviour, which …
* Tests: remove unused 'using'
* fix: tests work on Unity 2022.2+ \[imer]
* Weaver ILPostProcessorAssemblyResolver: use GetFileNameWithoutExtension
* perf: Weaver ILPostProcessorAssemblyResolver FindFile searches dllnam…
* Weaver: ILPostProcessorAssemblyResolver FindFile comments
* Weaver: ILPostProcessorAssemblyResolver caches FindFile for a 50x imp…
* perf: Weaver ILPostProcessorAssemblyResolver FindFile caches exeName,…
* Weaver ILPostProcessorAssemblyResolver: FindFile uses string name par…
* perf: Weaver ILPostProcessorAssemblyResolver uses ConcurrentDictionar…
* RigidbodyPhysics demo: disable 'onlySyncOnChange' until #3519 is fixed
* Examples/VR link
* Unbatcher: Unity 2019 fix
* fix: Batching VarInt size header added to fix custom serializated Net…
* feature: NetworkRigidbody Benchmark demo
* fix: NetworkRigidbody/2D now uses .target's Rigidbody and supports ch…
* NetworkRigidbody: MovePosition overwrite comments
* feature: NetworkMessageId <> Type lookup and log function for debuggi…
* Unbatcher: simplify GetNextMessage
* feature: VarInt size prediction
* NetworkMessages: keep GetId for convenience
* breaking: NetworkTransform renamed to NetworkTransformUnreliable for …
* remove duplicate NetworkRigidbody scripts
* NetworkTransform: moved all related scripts into one NetworkTransform…
* perf: NetworkRigidbody Reliable/Unreliable via NetworkTransform for s…
* fix: NetworkBehaviourInspector 'had no target' warning
* Readme: Information Security Guidelines
* NetworkTransform: show Gizmos as WriteCube instead of solid Cube for …
* Rigidbody Example: gray background so we can see overlays easier
* Rigidbody Example: show time interpolation GUI
* RigidbodyPhysics Example: fix missing component warnings
* fix: Weaver runtime fuse to ensure weaving succeeded before starting …
* Snapshot Interpolation Example: README
* CompilationFinishedHook: add explanation
* fix NetworkClientTests for Unity 2019
* NetworkMessages.WrapHandler: improve log message to make it easier to…
* fix: NetworkClient unbatcher is now reset in host mode connect as wel…
* Unbatcher: readonly fields
* fix: KcpServer.Stop now clears connections so they aren't carried ove…
* fix: fixing use of new c# in 2020
* feat: adding option to get Ip behind reverse proxy

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
