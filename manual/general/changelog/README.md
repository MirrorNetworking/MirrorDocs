# Change Log

{% hint style="info" %}
Mirror is published to the [Asset Store](https://assetstore.unity.com/packages/tools/network/mirror-129321) at the start of every month, unless some critical issue causes a delay.
{% endhint %}

{% hint style="info" %}
Mirror uses semantic versioning, and the versions shown here are those that were published to the Asset Store, and occasionally major version bumps happen mid-month between store submissions and are therefore not individually shown here.
{% endhint %}

## [v97.0.0](https://assetstore.unity.com/packages/tools/network/mirror-129321) -- In Progress

### Added

* NetworkTransform now has dropdown for using Update/FixedUpdate/LateUpdate
* RemoteStatistics now has option to skip password and auth check for convenient testing
* Mirror Render Pipeline Converter for URP compatibility
* NetworkName component: syncs object name from server to clients
* NetworkBehaviour::isHost shortcut for isServer + isClient
* UniqueNameAuthenticator: reusable version from the Chat example
* Card Game example with simple match making and tight data flow

### Fixes

* Network Authenticator UnityEvents improved and Unity 2019 compatibility restored
* Predicted Rigidbody no longer resets ghost objects
* NetworkScenePostProcess now only processes the last loaded scene instead of all loaded scenes for better performance
* NetworkServer / NetworkClient exceptionsDisconnect now is reset during Shutdown
* NetworkManager now unsubscribes from OnSceneLoaded in OnDestroy
* Some common code in various NetworkTransform flavors consolidated to NetworkTransformBase
* NetworkTransform now handles changing SendIntervalMultiplier at runtime
* NetworkTransform::OnTeleport no longer breaks delta compression
* NetworkTransformHybrid now better handles initial child sync
* NetworkAnimator Trigger message handling improved
* SimpleWebTransport now includes Port in ToString
* SimpleWebTransport now includes timestamps in log outputs
* SimpleWebTransport general improvements in logging
* SimpleWebTransport minimized calls to conn.Dispose
* InterestManagement now hides Canvas in SetHostVisibility
* OnGUI now properly excluded from Dedicated Server builds
* ReadOnly attribute now supports TextArea fields
* NetworkIdentity::AssignClientAuthority now ensures owner data is included
* CecilX updated with missing namespaces and build fixes
* Many examples have been updated, especially PlayerTest prefabs and controllers

### Changes

* NetworkAnimator: Deprecated clientAuthority for syncDirection
* Simple Web Transport no longer supports Unity 2021 and older
* **BREAKING**: Commands invoked by host client bypass network writer / reader
  * This makes the host client timing different from remote clients
  * This now requires careful testing with remote clients that may act differently
