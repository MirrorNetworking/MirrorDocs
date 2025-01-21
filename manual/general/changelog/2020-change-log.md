# 2020 Change Log

{% hint style="info" %}
Mirror uses semantic versioning, and the versions shown here are those that were published to the Asset Store, and occasionally major version bumps happen mid-month between store submissions and are therefore not individually shown here.
{% endhint %}

## [v30.2.2](https://github.com/MirrorNetworking/Mirror/releases/tag/v30.2.2) -- 2020-Dec-17 <a href="#version-3022----2020-dec-17" id="version-3022----2020-dec-17"></a>

* Added: Network Reader/Writer now does `ReadBlittable` / `WriteBlittable` for better performance.
* Added: KCP Transport updated to version 1.4.
* Added: Network Authenticator now has `OnStopServer` and `OnStopClient` overrides.
* Added: Network Transform now applies compression to rotations.
* Added: `SyncVars` now support using types that inherit from `NetworkBehaviour`.
* Added: `SimpleWebTransport` now has option for TLS outside the transport.
* Added: `MultipleMatches` example featuring Network Match Checker with Lobby and Match functionality.
* Fixed: Better presentation of Network Ping Display..
* Fixed: Middleware Transport now listens for inner events correctly..
* Fixed: Calling `StartServer` / `StartClient` more than once is now prevented..
* Fixed: NRE when no host player in `PlayerScore` in `MultipleAdditiveScenes` example.
* Fixed: player collisions in `MultipleAdditiveScenes` example.
* Fixed: Weaver now properly generates readers and writers for structs in other assemblies, e.g. `Mathematics`.
* Fixed: `NetworkServer.maxConnections` is now public.
* Fixed: Skip reset of object that's being destroyed.
* Fixed: `ReadyStatusChanged` in `NetworkRoomManager` is now public.
* Fixed: `NetworkTransform` now correctly uses `ArraySegment` in Commands.
* Fixed: Added `OnStopServer` to `NetworkSceneChecker` and `NetworkMatchChecker`.
* Fixed: Weaver method lookup conflict with Unity 2020.2.
* Fixed: Memory allocation vulnerability in `NetworkReader`.
* Changed: Play mode now blocked for invalid scene id's, with error messages.
* Changed: Experimental Network Transform now uses `Channels.DefaultUnreliable` and applies compression to rotations.
* Changed: Server Only scene objects in examples replaced with normal objects instantiated on server or just eliminated.
* **Breaking:** Force `NetworkMessage` to use structs in all cases - classes no longer supported.
* **Breaking:** Removed `ReadPacked` / `WritePacked` functions - Use Read and Write variants instead.
* **Breaking:** LLAPI has been removed.

## [v26.2.2](https://github.com/MirrorNetworking/Mirror/releases/tag/v26.2.2) -- 2020-Nov-20 <a href="#version-2622----2020-nov-20" id="version-2622----2020-nov-20"></a>

* Added: [KCP Transport](https://mirror-networking.com/docs/Articles/Transports/KCPTransport.html).
* Added: [Libuv2k Transport](https://mirror-networking.com/docs/Articles/Transports/Libuv2k.html).
* Added: Quaternion and float compression.
* Added: Option to configure handshakeMaxSize in SimpleWebTransport.
* Added: ServerTeleport to Experimental NetworkTransform.
* Added: Accept and Reject methods to Authenticator base class for ease of use.
* Fixed: Weaver failure on Authenticators and assemblies with only messages and no behaviors.
* Fixed: Examples no longer rely on sequential connectionId.
* Fixed: Eliminated errors when first importing due to preprocessor defines in transports.
* Fixed: Scene ID logging is now consistently in hex format.
* Fixed: sceneId when using BuildPipeline.BuildPlayer with incorrect case in path.
* Fixed: Trigger not applied correctly for host in NetworkAnimator.
* Fixed: Ensure ReadHelper.Read reads exactly number of bytes in SimpleWebTransport.
* Changed: Minimum Unity version is now 2018.4.28 LTS.
* Changed: Script Templates are now available as a separate package [here](https://mirror-networking.com/docs/Articles/General/ScriptTemplates.html).
* Changed: Telepathy Transport is now obsolete - Use [KCP Transport](https://mirror-networking.com/docs/Articles/Transports/KCPTransport.html) instead.
* **Breaking:** NetworkIdentity.clientAuthorityCallback is an event now.
* **Breaking:** Removed older obsolete methods and tests.
* **Breaking:** Transports no longer support sending to a List of connectionIds.

## [v23.0.3](https://github.com/MirrorNetworking/Mirror/releases/tag/v23.0.3) -- 2020-Oct-24 <a href="#version-2303----2020-oct-24" id="version-2303----2020-oct-24"></a>

* Added: Support null when serializing classes.
* Added: Support recursive data types.
* Added: Support jagged arrays.
* Added: New generic Read and Write methods for types used by mirror and user callbacks.
* Added: NetworkAnimator now syncs animator.speed.
* Added: NetworkTransform Server Teleport function.
* Added: New websockets transport: [SimpleWebTransport](https://github.com/MirrorNetworking/SimpleWebTransport).
* Fixed: ClientScene.localplayer is now set to null when it is destroyed.
* Fixed: Fixing IndexChanged hook not being called for NetworkRoomPlayer.
* Fixed: Fixing NullReferenceException when loading scene.
* Fixed: Fixing NetworkConnectionToClient for IL2CPP.
* Fixed: NullReferenceException in tanks example when running in headless server only.
* Changed: Use SyncLists/SyncSet/SyncDictionary directly, e.g. `SyncList`, see [Pull Request 2307](https://github.com/vis2k/Mirror/pull/2307).
* Changed: OnSerialize now includes the component index as byte before serializing each component. Better CPU performance at the cost of bandwidth.
* Changed: Can now have 256 NetworkBehaviours per NetworkIdentity.
* **Breaking**: Renamed `IMessageBase` to `NetworkMessage`, see [Pull Request 2317](https://github.com/vis2k/Mirror/pull/2317).
* **Removed**: SyncEvents, see [Pull Request 2178](https://github.com/vis2k/Mirror/pull/2178).
* **Removed**: Removing manual invoke for Cmd and RPC, see [Pull Request 2223](https://github.com/vis2k/Mirror/pull/2223).
* **Removed**: Removed Serialize/Deserialize in messages, see [Pull Request 2317](https://github.com/vis2k/Mirror/pull/2317).
* **Removed**: Removing auto initialize for SyncLists/SyncSet/SyncDictionary, see [Pull Request 2330](https://github.com/vis2k/Mirror/pull/2330).
* **Removed**: Replacing [Ninja.WebSockets](https://github.com/MirrorNetworking/NinjaWebSocketsTransport) with [SimpleWebTransport](https://github.com/MirrorNetworking/SimpleWebTransport).

## [v17.3.0](https://github.com/MirrorNetworking/Mirror/releases/tag/v17.3.0) -- 2020-Sep-04 <a href="#version-1730----2020-sep-04" id="version-1730----2020-sep-04"></a>

* Added: NetworkAnimator now syncs Layer Weight.
* Added: Lists can now be sent in Command/Rpc/Message/etc.
* Added: `[Server]`/`[Client]` can now be used outside of NetworkBehaviour.
* Added: Experimental [NetworkLerpRigidbody](https://mirror-networking.com/docs/Articles/Components/NetworkLerpRigidbody.html) component that syncs position and velocity and applies them using Lerp.
* Fixed: NetworkAnimator now fires triggers immediately on owner.
* Fixed: `isServer` will now keep its value after calling `NetworkServer.Destroy`.
* Fixed: `[Client]` error message now correctly logs the name of the method.
* Fixed: Messages can now be nested within other Message types.
* Fixed: `[Server]`/`[Client]` now correctly give error when used on abstract method.
* Fixed: Abstract classes can now implement IMessageBase.
* Fixed: Weaver now correctly gives error when generating a reader methods for abstract classes.
* Changed: NetworkServer no longer runs update if there are no connections.
* Changed: NetworkBehaviour.IsDirty is now a public.
* Changed: SyncEvent are now obsolete and will be removed in the next version.
* **Removed**: isHeadless is now obsolete, Use preprocessor directive `#if UNITY_SERVER` instead.

## [v16.9.0](https://github.com/MirrorNetworking/Mirror/releases/tag/v16.9.0) -- 2020-Aug-01 <a href="#version-1690----2020-aug-01" id="version-1690----2020-aug-01"></a>

* Added: [NetworkHeadlessLogger](https://mirror-networking.com/docs/Articles/Components/NetworkHeadlessLogger.html) log handler that sets console color.
* Added: New Mirror List Server, see [Cloud folder](https://github.com/vis2k/Mirror/tree/master/Assets/Mirror/Cloud).
* Added: Experimental [NetworkRigidbody](https://mirror-networking.com/docs/Articles/Components/NetworkRigidbody.html) component that syncs velocity and other rigidbody settings.
* Fixed: base method called inside Command/RPC now work if the first base class does not have an override.
* Fixed: NetworkRoomPlayer now cleans up roomSlots on disable.
* Fixed: Fallback and Multiplex now disable their transports when they are disabled.
* Fixed: Websockets transport SocketState now returns false if socket is `undefined`.
* Fixed: SyncEvents can now have the same name if they are in different classes.
* Fixed: You can now have multiple SyncEvents per class.
* Fixed: Message base classes are now processed even if they are declared later in the file.
* Fixed: Registering a prefab with and same GUID no longer gives an error.
* Fixed: Weaver now generates Serialize methods for classes that implement IMessageBase.
* Changed: NetworkProximityChecker now has slightly better performance.
* Changed: ClientRpc no longer need Rpc prefix.
* Changed: Commands no longer need Cmd prefix.
* Changed: TargetRpc no longer need Target prefix.
* Changed: NetworkManager.networkSceneName is now protected set as it should not be set directly.

## [v16.1.1](https://github.com/MirrorNetworking/Mirror/releases/tag/v16.1.1) -- 2020-Jun-13 <a href="#version-1611----2020-jun-13" id="version-1611----2020-jun-13"></a>

* Added: \[Command] now has an `ignoreAuthority` option for invoking Commands on objects the client doesn't have authority over, and Command methods can have an optional `NetworkConnectionToClient sender` parameter.
* Added: \[ClientRpc] now has an `excludeOwner` option to prevent messages from going to the client that owns the object.
* Added: Commands and Rpc's can now be declared as virtual and overridden in derived classes.
* Added: NetworkLogSettings component and Log Settings Window.
* Added: SyncLists now support `AddRange`, `InsertRange`, and `RemoveAll`.
* Added: Network Room Manager now has a virtual `OnRoomServerPlayersNotReady` that fires on server from `CheckReadyToBegin`.
* Added: Network Room Player template now includes base Network Behaviour overrides.
* Added: Network Room Player now has a virtual hook for the index SyncVar, and the override is in the template.
* Added: Experimental Network Transform with more control settings and features.
* Added: [Network Ping Display](https://mirror-networking.com/docs/Articles/Components/NetworkPingDisplay.html) component
* Fixed: Network Room Manager.minPlayers is now protected so it's available for derived classes.
* Fixed: Network Room Manager no longer does redundant player prefab registration in `OnStartClient`.
* Fixed: Network Room Player `OnClientEnterRoom` now correctly only fires on clients.
* Fixed: `RegisterClientMessages` was being incorrectly invoked for every scene change.
* Fixed: Network Behaviour `SyncMode` and `SyncInterval` was not showing in the inspector in some cases (regression).
* Fixed: Network Manager now cleans up network objects better when server stops.
* Fixed: Network Manager no longer tries to change to offline scene redundantly in `StopClient`.
* Fixed: Network Connection's `lastMessageTime` wasn't being properly initialized, causing incorrect idle timeout disconnects.
* Fixed: Websockets transport now correctly pauses and queues messages during scene changes and should otherwise be more stable with SSL
* Changed: Improvements were made to the Tanks example.
* Changed: Network Room Player now uses virtual SyncVar hook for ReadyStateChanged.
* Changed: Network Proximity Checker now uses direct distance check against player objects instead of Physics.SphereCastNonAlloc for better performance.
* **Breaking:** Websockets Transport now requires full path to PFX Certificate file.
* **Removed**: Discord Transport

## [v13.0.1](https://github.com/MirrorNetworking/Mirror/releases/tag/v13.0.1) -- 2020-May-06 <a href="#version-1301----2020-may-06" id="version-1301----2020-may-06"></a>

* Added: [Multiple Concurrent Additive Scenes Example](https://mirror-networking.com/docs/Articles/Examples/MultipleAdditiveScenes/index.html).
* Added: [Network Match Checker](https://mirror-networking.com/docs/Articles/Components/NetworkMatchChecker.html) component. Use this component to create many concurrent isolated matches within a single game server.
* Added: [SyncLists](https://mirror-networking.com/docs/Articles/Guides/Sync/SyncLists.html) now have Find and FindAll functions.
* Added: Network Behaviour now has `OnStopServer` and `OnStopClient` virtual methods.
* Added: Weaver now supports custom Reader & Writer for types in other assemblies.
* Added: Network Manager now has an optional setting to check for and disconnect remote connections that have gone silent for a specified interval.
* Added: Network Server and Network Client now have `ReplaceHandler` methods to avoid warnings when attempting to double register message handlers.
* Added: `NetworkServer.SendToAll` now has an optional bool to only send to ready clients, and a `SendToReady` method that doesn't require a Network Identity.
* Fixed: Network Animator no longer double-fires SetTrigger / ResetTrigger on the host client.
* Fixed: Network Animator is no longer limited to one component per object.
* Fixed: Destroy is no longer invoked twice on the server for the player object.
* Fixed: `RegisterClientMessages` is no longer invoked twice on the client.
* Fixed: Network Behaviour `SyncMode` and `SyncInterval` was not showing in the inspector in some cases.
* Fixed: Telepathy Transport - `LateUpdate` now processes a limited amount of messages per tick to avoid deadlocks.
* Changed: Network Behaviour: `OnNetworkDestroy` was renamed to `OnStopClient`.
* **Breaking:** RemovePlayerMessage has been removed as a potential security risk. Use `NetworkServer.RemovePlayerForConnection` instead.
* **Breaking:** Network Behaviour: `OnRebuildObservers`, `OnCheckObserver`, and `OnSetHostVisibility` were moved to a separate class called `NetworkVisibility`.

## [v11.4.2](https://github.com/MirrorNetworking/Mirror/releases/tag/v11.4.2) - 2020-Apr-03 <a href="#version-1142---2020-apr-03" id="version-1142---2020-apr-03"></a>

* Added: SyncVar hooks can be virtual now, and overridden in a derived class.
* Added: Virtual `OnRoomStopServer` added to Network Room Manager and Script Template.
* Added: 10K Networked Objects Benchmark Example.
* Fixed: Setting breakpoints in an IDE for Command's and Rpc's work correctly now.
* Fixed: `NetworkServer.SendToObservers` now reports correct channel to Mirror Profiler.
* Fixed: Network Room Manager's `roomPlayerPrefab` is now `protected` so it can be accessed in derived classes.
* Fixed: Network Room Player inspector and documentation updated to be less confusing.
* Fixed: Network Identity no longer double calls `NetworkServer.Destroy`.
* Fixed: Network Animator now correctly excludes parameters controlled by curves.
* Fixed: Network Behaviour now uses a property drawer for the SyncVar label so it displays better.
* Fixed: `NetworkServer.SendToReady` overloads are no longer ambiguous.
* Fixed: Network Room Manager no longer incorrectly destroys the game player object. It's left in the game scene to be cleaned up by Unity when the scene changes.
* Fixed: `StopHost` correctly raises `OnServerDisconnect` in Network Manager, and correctly unwinds before shutting down the server.
* Fixed: `isServer` is no longer incorrectly false on server in Network Identity's `OnDestroy`.
* Changed: Network Manager `offlineScene` and `onlineScene` store full paths now, so use SceneManager.GetActiveScene().path instead.
* Changed: Network Manager HUD now calls `StopHost` / `StopServer` / `StopClient` more appropriately.
* Changed: Network Manager HUD labels no longer say LAN. Associated docs also cleaned up to eliminate the misconception of Mirror being LAN only solution.
* Changed: Network Transform compression removed and message handling is much simpler now.
* Changed: Network Scene Checker initializes in Awake again because `OnEnable` proved to be unreliable in some cases.
* Changed: Network Manager will no longer lose references to scenes if they aren't in the Build Settings scene list, however moving or renaming scenes may cause references to be lost.
* **Removed:** Many obsolete methods and properties. Use [version 10](https://github.com/vis2k/Mirror/releases/tag/v10.4.7) first if upgrading from UNet or older Mirror. See [Deprecations](https://mirror-networking.com/docs/Articles/General/Deprecations.html) for complete list.

## [v10.4.7](https://github.com/MirrorNetworking/Mirror/releases/tag/v10.4.7) - 2020-Mar-03 <a href="#version-1047---2020-mar-03" id="version-1047---2020-mar-03"></a>

* Added: Weaver will now block play mode and builds if there are weaver errors and show them in the console again.
* Added: `PooledNetworkReader` and `PooledNetworkWriter`, both Disposable.
* Added: `NetworkReader.ReadMessage`.
* Added: Network Discovery now handles headless server mode.
* Added: SyncVar, Cmd's and Rpc's now support Scriptable Objects via CreateInstance on the receiving side.
* Added: Discord Transport _Removed in v16.1.1_
* Fixed: `isClient` now returns true on clients in OnDestroy for networked objects.
* Fixed: Host Player race condition for Ready message.
* Fixed: Network Animator and Network Transform now correctly check for client authority in their respective Command methods.
* Fixed: Network Room Manager Script Template had a virtual method instead of an override.
* Fixed: Network Server's calls to NetworkConnectionToClient.Send now includes the channelId parameter that was missing.
* Fixed: Network Server's calls to SendToAll, SendToReady, and SendToObservers send to the exact connection if it is detected as local connection, instead of falling back to the .localConnection.
* Fixed: Network Server.SpawnObjects returns false if server isn't running.
* Fixed: Network Transform rotation detection improved.
* Fixed: Weaver generated code now builds properly for IL2CPP (again).
* Changed: StartHost in Network Manager is no longer a virtual method (and shouldn't have been). Override OnStartHost instead.
* Changed: Network Room Manager's OnRoomServerSceneLoadedForPlayer now includes NetworkConnection parameter.
* Changed: Network Scene Checker now works from OnEnable instead of Awake, and uses Scene instead of scene name.
* Changed: Renamed NeworkWriter.Write to WriteMessage for consistency.

## [v9.0.2](https://github.com/MirrorNetworking/Mirror/releases/tag/v9.0.2) - 2020-Feb-04 <a href="#version-902---2020-feb-04" id="version-902---2020-feb-04"></a>

* Added: Network Animator now has a ResetTrigger function and server / client authority warnings.
* Added: Network Transform now has 3 new floats for Sensitivity to quiet down message traffic from micro changes.
* Added: Network Observer added to [Script Templates](https://mirror-networking.com/docs/Articles/General/ScriptTemplates.html) -- See the new Mirror section in the Assets > Create menu.
* Added: [Network Discovery](https://mirror-networking.com/docs/Articles/Components/NetworkDiscovery.html) has been reimplemented including an example and script template -- thanks to all those who contributed!
* Added: [Network Scene Checker Component](https://mirror-networking.com/docs/Articles/Components/NetworkSceneChecker.html).
* Added: Mirror Icon for all components.
* Added: Inspector Headers to Network Behaviour, Network Transform, and Network Animator.
* Added: URI added to supported data types.
* Added: `NetworkReaderPool` has been implemented in place of `new NetworkReader` everywhere, significantly reducing garbage allocation.
* Fixed: Network Transform and Network Animator now uses NetworkWriterPool.
* Fixed: Network Transform and Network Animator now respect `hasAuthority` for client owned objects.
* Fixed: Network Transform will now correctly teleport if time / distance are too large.
* Fixed: Network Animator now correctly syncs when clientAuthority is false.
* Fixed: Client owned objects are now destroyed when client disconnects (regression).
* Fixed: Client authority is now preserved for networked objects carried through scene change in DontDestroyOnLoad (DDOL).
* Fixed: Starting server immediately after canceling client connection attempt no longer throws a NRE.
* Fixed: IL2CPP builds no longer trigger an assert when stopping server.
* Fixed: SyncVars are now set for Host player before hook is invoked.
* Fixed: SyncDictionary now correctly updates the value before invoking callbacks.
* Fixed: StopHost no longer tries to change to the Offline scene twice.
* Fixed: Network Room Manager roomSlots are now correctly managed on both server and clients.
* Fixed: Network Room Manager now correctly supports multiple GamePlay scenes and adds a virtual `OnRoomServerAddPlayer`.
* Fixed: Additive scene operations no longer incorrectly lead to extraneous AddPlayer messages from clients.
* Fixed: `NetworkWriterPool` is now used everywhere instead of `new NetworkWriter` (we missed a couple when the pool was implemented).
* Fixed: Patch for Unity 2019.3.x `RequestScriptReload` was moved to EditorUtility from UnityEditorInternal.InternalEditorUtility.
* Fixed: Arrays of custom types are now correctly supported.
* Changed: Shutdown logic has been streamlined.
* Changed: `NetworkIdentity.GetSceneIdenity` method renamed to `GetSceneIdentity` (name typo).
* Changed: OnApplicationQuit virtual method added to Transport class and `Transport.activeTransport.Shutdown()` is no longer called from Network Manager.
* **Breaking:** SyncVar Hooks now require **two** parameters, one each for the old and new value, and the property value is now set **before** the hook is called.

## [v6.7.7](https://github.com/MirrorNetworking/Mirror/releases/tag/v6.7.7) - 2020-Jan-01 <a href="#version-677---2020-jan-01" id="version-677---2020-jan-01"></a>

* Added: [Script Templates](https://mirror-networking.com/docs/Articles/General/ScriptTemplates.html) -- See the new Mirror section in the Assets > Create menu.
* Added: Full Text Search added to docs.
* Added: Basic Chat example.
* Added: Some YouTube videos have been created and linked from various doc pages where appropriate.
* Added: Transports can now support using a URI for connection including port.
* Added: version.txt file is now included with the release version in it.
* Added: Structs that inherit from IMessageBase now generate serializers.
* Fixed: Components now appear in docs under API Reference.
* Fixed: Delayed disconnect in Basic Authenticator.
* Fixed: Multiplexer now handles OnClientConnect and GetMaxMessageSize correctly.
* Fixed: Network Room Manager OnRoomServerCreateGamePlayer now includes the Room Player Object as parameter.
* Changed: Room example now has Network Room Manager in the Offline scene and correctly switches to the Room scene when server / client is started.
* Changed: Network Manager startPositionIndex and loadingSceneAsync are now public.
* Changed: SceneMessage now has an optional customHandling flag so devs can handle their own custom scene loading, e.g. using Addressables.
* **Breaking:** Network Transform now defaults to server authority and has a Client Authority checkbox.
