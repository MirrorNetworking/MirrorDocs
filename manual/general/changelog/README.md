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

## v90.x.x -- In Progress

### Added

* Added: Transport: `IsEncrypted` and `EncryptionCypher` virtual methods.
* Added: Encryption Transport implements`IsEncrypted` & `EncryptionCypher` .
* Added: Simple Web Transport implements`IsEncrypted` & `EncryptionCypher` .
* Added: Edgegap Lobby Transport & Demo.

### Fixed

* Fixed: Weaver Custom Read / Write methods for Serialization now works across assembly definitions.
* Fixed: EdgegapKcp StopHost() null reference exception.
* Fixed: ILPostProcessorAssemblyResolver now ignores Bee.BeeDriver better.
* Fixed: Simple Web Transport now locally scopes `websocket` and `Runtime`.

### Changed

* Changed: Continued improvements to `Prediction` and `PredictedRigidbody`...still experimental.

## v89.0.0 -- 2024-Mar-05

### Added

* Added: `GetFunctionMethodName` added to `RemoteProcedureCalls`.
* Added: `NetworkClient` `RegisterHandler` now takes `ChannelId` parameter.
* Added: `NetworkServer` and `NetworkClient` now have `ReplaceHandler` with `ChannelID` parameter.
* Added: SyncVar now supports arrays.
* Added: `NetworkTime.SendPing` method for `NetworkClient` to call from `OnTransportConnected`.
* Added: NetworkTransform-Unreliable only send changed data for improved bandwidth usage.
* Added: [Lag Compensator](../lag-compensation.md) as convenience component that wraps all the Lag Compensation logic.
* Added: [Encryption Transport](../../transports/encryption-transport.md) as chained transport.

### Fixed

* Fixed: `NetworkServer.RemovePlayerForConnection` now uses `NetworkConnectionToClient`.
* Fixed: `NotReadyMessage` no longer requires authenticated client.
* Fixed: `InterestManagementBase` `OnEnable` no longer shows error when called multiple times.
* Fixed: Match and Team Interest Management overhauled for better performance.
* Fixed: `NetworkAnimator` now initializes state in `OnEnable`.
* Fixed: `NetworkAnimator` now always serializes and deserializes the exact amount of data.
* Fixed: `NetworkAnimator` `ClientRpc` now has `includeOwner = false`.
* Fixed: NetworkServer.SendToAll/Observers/ReadyObservers now validates packet size while \<T> is still known.
* Fixed: NetworkTransform-Unreliable Quaternion compression.
* Fixed: `NetworkManager.ServerChangeScene` now prevents client from calling.
* Fixed: `NetworkManager` no longer forces `networkAddress` to `localhost` on server builds.
* Fixed: Multiplexer now avoids `KeyNotFoundException`.
* Fixed: NetworkServer and NetworkClient respect exceptionDisconnect.
* Fixed: Now uses `FindAnyObjectByType` for later Unity versions.
* Fixed: `NetworkServer.Destroy` refactored.
* Fixed: `NetworkServer.Spawn` now activates inactive GameObjects (in case Unspawned).

### Changed

* Changed: KCP Transport updated to version 1.40.
* Changed: `InterestManagement` now has separate `ResetState` from `Reset`.
* Changed: `NetworkTransform` now has separate `ResetStat`e from `Reset`.
* Changed: `NetworkTransform` Sync Direction now defaults to Client To Server.
* Changed: `NetworkAnimator` Sync Direction now defaults to Client To Server.
* Changed: `NetworkRigidbody` Sync Direction now defaults to Client To Server.
* Changed: `onlySyncOnChange` and `compressRotation` moved to `NetworkTransformBase`.
* Changed: Extensive improvements to `Prediction` and `PredictedRigidbody`...still experimental.
* Changed: Examples Updated.
* Changed: [Script Templates](../script-templates.md) Updated.
* Changed: Edgegap Hosting Updated.
* **Breaking Change:** `NetworkIdentity` `visible` renamed to `visibility`.

