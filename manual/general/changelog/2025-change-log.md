# 2025 Change Log

{% hint style="info" %}
Mirror is published to the [Asset Store](https://assetstore.unity.com/packages/tools/network/mirror-129321) at the start of every month, unless some critical issue causes a delay.
{% endhint %}

{% hint style="info" %}
Mirror uses semantic versioning, and the versions shown here are those that were published to the Asset Store, and occasionally major version bumps happen mid-month between store submissions and are therefore not individually shown here.
{% endhint %}

## [v96.0.1](https://assetstore.unity.com/packages/tools/network/mirror-129321) -- 2025-Feb-27

### Added

* [Hex Spatial Hash Interest Management](../../interest-management/hex-spatial-hashing.md) 2D and 3D with additional optimizations.
* Network Manager now has a Clear button for spawnable prefabs.
* Scene Distance Interest Management (combines Scene and Distance IM's).
* Interest Management SetHostVisibility now handles terrains with tree details, lights, audio sources, and particle systems correctly.
* NetworkTransform now exposes `velocity` and `angularVelocity` on the receiving side.
* Encryption Transport now logs hardware accelleration status.
* PlayerControllerRB in example controllers: uses non-kinematic rigidbody instead of Character Controller.
* Vector4Long implemented for quarternions delta compression.
* Half Floats for Quaternion compression.
* Entering play mode while in prefab edit mode works now.
* HashSet is now supported in SyncVar / Command / ClientRpc / TargetRpc / NetworkMessage.
* NetworkIdentity.OnDestroy verifies spawned\[netId] before removing.
* NetworkTransformHybrid that emits Reliable full state on a slow interval and Unreliable deltas in between.
* PlayerTest example now has PlayerContollerHybrid prefab for testing.
* PickupsDropsChilds example now has interface implemented to show how that should be done.
* HashSet is now a supported type for SyncVar / Cmd / Rpc.
* HalfFloats added to Compression.

### Fixes

* NetworkClient now defers spawn payload until after all objects have been created, eliminating many common race conditions.
  * Fixed bug in 96.0.1 for missing message.payload.count check.
* Reader / Writer Processor now works across assemblies.
* Simple Web Transport jslib improved to support more platforms.
* Network Manager StopClient no longer calls OnClientDisconnectInternal (Transport calls it).
* NetworkServer now correctly calls NetworkClient.InvokeUnSpawnHandler from UnSpawnInternal
* NetworkAnimator no longer double calls HandleAnimSetTrigger / HandleAnimResetTrigger on host client.
* NetworkTransform now calls Physics.SyncTransforms in ResetState, which is called from OnTeleport, making it easier to teleport without special handling for character controllers and non-kinematic rigidbodies that would resist being repositioned.
* NetworkTransform now supports FixedUpdate, so non-kinematic rigidbody players don't stutter.
* All forms of Interest Management now use LateUpdate.
* Netgraph improvements.
* Examples updated.
* Edgegap Plugin updated to 2.3.1.
* Encryption Transport now implements Port Transport.
* Network Server now checks `listen` and rejects connections if not listening.
* KCP handles nulls in OnClientError during shutdown better now.
* Room and Multiple Additive Scene examples now demonstrate Pooling (See Spawner and Reward scripts).
* Portal script in Additive Levels example now properly waits for RemovePlayerForConnection to complete.
* PredictionUtils now correctly sets freeze rotation before constraints.
* Entering playmode when in prefab edit mode is no longer prevented (now it works).

### Changes

* NetworkClient, for initial spawning, now defers applying spawn payload until after instantiating all networked objects and adding them into the `spawned` dictionary. This means SyncVars and Sync\* collections that cross-reference other networked objects should be much more robust, as the long-standing race condition should now be eliminated.
* Network Server `dontListen` renamed to `listen` to eliminate double negative bool.
* Moved connectionId to NetworkConnectionToClient (it's meaningless on clients).
* Deprecated Network Manager's virtual OnApplicationQuit - Use OnDestroy instead.
* Deprecated Transport's virtual OnApplicationQuit - Use OnDestroy instead.
* Deprecated Snapshot interpolation settings in Network Client in favor of SnapShotSettings.
* Bouncy Castle moved to Encryption Transport folder.
