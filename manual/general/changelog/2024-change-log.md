# 2024 Change Log

{% hint style="info" %}
Mirror is published to the [Asset Store](https://assetstore.unity.com/packages/tools/network/mirror-129321) at the start of every month, unless some critical issue causes a delay.
{% endhint %}

{% hint style="info" %}
Mirror uses semantic versioning, and the versions shown here are those that were published to the Asset Store, and occasionally major version bumps happen mid-month between store submissions and are therefore not individually shown here.
{% endhint %}

## [v93.0.0](https://github.com/MirrorNetworking/Mirror/releases/tag/v93.0.0) - 2024-Oct-5

### Added

* Unity 6 Compatibility.
* PlayerContollers have been overhauled and examples updated.
* ServerTeleport added to NetworkTransform.
* Ping/Traffic/FPS Graphs.
* PickupsDropsChilds example with Robot Kyle created.

### Fixes

* Edgegap Plugin updated to 1.0.8.
* Simple Web Transport improved logging and removed allocations.
* Telepathy Transport now has more robust error handling in GetClientAddress.
* ReadOnly attribute now works for properties too.

### Changes

### Breaking Changes

* NetworkTransform sendIntervalMultiplier replaced by syncInterval math.
* Older obsoletes removed.

## [v90.0.0](https://github.com/MirrorNetworking/Mirror/releases/tag/v90.0.0) - 2024-Aug-2

### Added

* TopDown Shooter example created.
* AutoLAN exxample created.
* NetworkRuntimeProfiler implemented.
* OnServerConnectedWithAddress implemented for all Transports.
* Network Manager now has `offlineSceneLoadDelay` to allow showing a UI message before loading the offline scene.

### Fixes

* GetPositionAndRotation extension now excluded for 2022.0 or newer.
* Edgegap Plugin updated to 1.0.1.
* NetworkServer.Destroy no longer calls ResetState on prefabs so isServer is preserverd for OnDestroy.
* NetworkBehaviour now checks for non-zero netId before attempting to invoke Commands.
* NetworkServer now removes default OnTransportConnectedWithAddress handler in RemoveTransportHandlers.
* Simple Web Transport now supports https URI scheme.
* TimeSnapshotMessage no longer requires authority.

### Changes

* Bouncy Castle now has new namespace and GUID.
* Simple Web Transport default setup now assumes reverse proxy will be used.
* OnServerConnected has been deprecated in favor of OnTransportConnectedWithAddress.

## [v89.80](https://github.com/MirrorNetworking/Mirror/releases/tag/v89.8.0) - 2024-Jun-13

### Added

* Transport: `IsEncrypted` and `EncryptionCypher` virtual methods.
* Encryption Transport implements`IsEncrypted` & `EncryptionCypher` .
* Simple Web Transport implements`IsEncrypted` & `EncryptionCypher` .
* Edgegap Lobby Transport & Demo.
* Transport Exception Actions have been reimplemented.
* SpatialHashing3D for XYZ to include vertical axis in checks.
* Implement PortTransport for LatencySimulation since almost all underlying Transports are PortTransports.

### Fixes

* Weaver Custom Read / Write methods for Serialization now works across assembly definitions.
* EdgegapKcp StopHost() null reference exception.
* EdgegapHosting now only includes enabled scenes in builds.
* ILPostProcessorAssemblyResolver now ignores Bee.BeeDriver better.
* Simple Web Transport now locally scopes `websocket` and `Runtime`.
* Latency Simulation had a bunch of minor bugs.
* Missing overrides added to Network Rigidbody components.
* Network Manager's OnGUI now wrapped in `DEBUG` compiler symbol.
* NetworkServer now correctly spawns player for owner client when Visibility is Force Hidden.
* Network Animator now defaults animatorSpeed to 1 so SyncVar works correctly.
* NetworkTransform components auto-assign their own GO if target is unassigned.
* Pool now checks for nulls when returning and discards.
* Batches are now properly returned to NetworkWriterPool before destroying the connection
* GetSyncVarNetworkBehaviour now validates componentIndex and logs detailed error instead of IndexOutofRangeException.
* Sync\* Collections now have pretty names in inspector.
* version file is now included in release packages with correct version.

### Changes

* `LagCompensator` attributes updated and custom inspector added.
* Remote Statistics default hotkey changed to backtick ( `` ` `` ).
* Continued improvements to `Prediction` and `PredictedRigidbody`. These are still considered as experimental features.
* SyncDictionary, SyncList, and SyncSet all now call their Clear operations **after** invoking the Callback, so users can iterate the collection in the callback hander.
* SyncDictionary, SyncList, and SyncSet all now have individual Actions that users can subscribe to directly.
  * **NOTE:** Some Actions pass the **OLD** values where appropriate, so be careful with your implementations. Also note that these new actions replace the generic Callback Action that is now obsolete and will be removed later in the year.
* KCP Transport updated to version 1.41.
* Moved GetClientAddress exception handling from Transport into Telepathy itself.
* NetworkServer.RemovePlayerForConnection now has RemovePlayerOptions.

## [v89.0.0](https://github.com/MirrorNetworking/Mirror/releases/tag/v89.0.0) -- 2024-Mar-05

### Added

* `GetFunctionMethodName` added to `RemoteProcedureCalls`.
* `NetworkClient` `RegisterHandler` now takes `ChannelId` parameter.
* `NetworkServer` and `NetworkClient` now have `ReplaceHandler` with `ChannelID` parameter.
* SyncVar now supports arrays.
* `NetworkTime.SendPing` method for `NetworkClient` to call from `OnTransportConnected`.
* NetworkTransform-Unreliable only send changed data for improved bandwidth usage.
* [Lag Compensator](../lag-compensation.md) as convenience component that wraps all the Lag Compensation logic.
* [Encryption Transport](../../transports/encryption-transport.md) as chained transport.

### Fixes

* `NetworkServer.RemovePlayerForConnection` now uses `NetworkConnectionToClient`.
* `NotReadyMessage` no longer requires authenticated client.
* `InterestManagementBase` `OnEnable` no longer shows error when called multiple times.
* Match and Team Interest Management overhauled for better performance.
* `NetworkAnimator` now initializes state in `OnEnable`.
* `NetworkAnimator` now always serializes and deserializes the exact amount of data.
* `NetworkAnimator` `ClientRpc` now has `includeOwner = false`.
* NetworkServer.SendToAll/Observers/ReadyObservers now validates packet size while \<T> is still known.
* NetworkTransform-Unreliable Quaternion compression.
* `NetworkManager.ServerChangeScene` now prevents client from calling.
* `NetworkManager` no longer forces `networkAddress` to `localhost` on server builds.
* Multiplexer now avoids `KeyNotFoundException`.
* NetworkServer and NetworkClient respect exceptionDisconnect.
* Now uses `FindAnyObjectByType` for later Unity versions.
* `NetworkServer.Destroy` refactored.
* `NetworkServer.Spawn` now activates inactive GameObjects (in case Unspawned).

### Changes

* KCP Transport updated to version 1.40.
* `InterestManagement` now has separate `ResetState` from `Reset`.
* `NetworkTransform` now has separate `ResetStat`e from `Reset`.
* `NetworkTransform` Sync Direction now defaults to Client To Server.
* `NetworkAnimator` Sync Direction now defaults to Client To Server.
* `NetworkRigidbody` Sync Direction now defaults to Client To Server.
* `onlySyncOnChange` and `compressRotation` moved to `NetworkTransformBase`.
* Extensive improvements to `Prediction` and `PredictedRigidbody`...still experimental.
* Examples Updated.
* [Script Templates](../script-templates.md) Updated.
* Edgegap Hosting Updated.

### Breaking Changes

* `NetworkIdentity` `visible` renamed to `visibility`.
