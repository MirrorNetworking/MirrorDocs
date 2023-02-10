# 2019 Change Log

{% hint style="info" %}
Mirror uses semantic versioning, and the versions shown here are those that were published to the Asset Store, and occasionally major version bumps happen mid-month between store submissions and are therefore not individually shown here.
{% endhint %}

## v6.3.0 - 2019-Dec-09 <a href="#version-630---2019-dec-09" id="version-630---2019-dec-09"></a>

* Added: SyncMode selector now works for components on any scene objects owned by a client in addition to the player object, e.g. pets.
* Added: MessageBase now fully supports inheritance.
* Added: Room example now has UI button for host to return all clients to the Room scene and other improvements.
* Fixed: ReplacePlayerForConnection now works for existing scene objects as long as another player doesn't own the targeted object.
* Fixed: isClient and isServer are now true for networked objects in Start and OnStartClient / OnStartServer, respectively.
* Fixed: hasAuthority is now set before OnStartClient and OnStartLocalPlayer are invoked.
* Changed: connectionToClient is now used to assign client authority.
* Changed: In many respects, the Host player acts a lot more like a client and will reveal bugs better.
* Changed: ReplacePlayerForConnection now has an optional bool to retain authority of the previous object (default = false).
* Removed: `NetworkServer.SpawnWithClientAuthority` is deprecated in favor of overloads of `NetworkServer.Spawn`.

## v5.0.2 - 2019-Nov-03 <a href="#version-502---2019-nov-03" id="version-502---2019-nov-03"></a>

* Added: SyncList and SyncSet custom Equality Comparer support.
* Added: Custom serializers may be implemented for any type in Cmd's and Rpc's.
* Added: [Fallback Transport](https://mirror-networking.com/docs/Articles/Transports/Fallback.html).
* Fixed: SyncVar hooks are no longer called in Host if no change.
* Fixed: Network Identity no longer throws a null reference exception in RemoveClientAuthority.
* Fixed: Server transport now suspended during scene change to prevent erroneous messages.
* Fixed: SyncList, SyncDictionary and SyncSet now use a custom IEnumerator to avoid memory allocation.
* Fixed: sceneID is no longer reset in certain cases when editing a prefab.
* Changed: PreprocessorDefine code moved to CompilerSymbols folder to avoid paradox of missing symbols preventing the symbols being added to the project.
* Changed: Host player no longer gets authority assigned to all objects by default.
* Changed: Commands no longer bypass serialization for Host player, making debugging easier.
* Changed: Local connections now maintain their own message queue.
* Changed: Transport.Available is now abstract.
* Removed: Network Identity: Local Player Authority has been removed as no longer necessary.

## v4.0.7 - 2019-Oct-03 <a href="#version-407---2019-oct-03" id="version-407---2019-oct-03"></a>

* Added: [Authenticators](https://mirror-networking.com/docs/Articles/Components/Authenticators/index.html) support to authenticate clients in the Connect phase.
* Added: Profiler events. These events can be subscribed to by the [Network Profiler](https://mirror-networking.com/docs/Articles/Guides/Profiler.html) to provide visual information.
* Added: Transports now include channel in profiler events.
* Added: Transport abstract class now supports sending a message to a list of connection id's.
* Fixed: SceneMessage now has sceneOperation enum so clients can properly handle additive scenes.
* Fixed: NetworkClient handlers are now cleared in Shutdown.
* Fixed: Offline scene is no longer reloaded when client fails to connect or is rejected.
* Fixed: Start Position Index is now reset to zero when server is stopped.
* Fixed: Network Room Players are now all in DontDestroyOnLoad so they don't respawn in the game scene.
* Fixed: Network Room example player controller restores main camera on disable.
* Fixed: Components with different sync intervals were not sending updates to clients.
* Fixed: In certain cases, weaver wouldn't weave some external assemblies.
* Fixed: Network Animator now does a full sync for new clients.
* Fixed: Network Behaviour inspector now shows SyncMode for private SyncVars.
* Fixed: Calling Commands and Rpcs of parent classes from inherited classes works as it should.
* Fixed: Telepathy no longer hangs when attempting to connect to a nonexistent host.
* Fixed: Websockets Transport now properly returns the client endpoint information via `ServerGetClientAddress`.
* Fixed: WebGL build now works with ArraySegment.
* Changed: Mirror is now free of garbage memory allocation on the sending side.
  * Some transports may still have a little garbage allocation yet.
* Changed: Deprecated the AddPlayerMessage extraMessage byte\[] in favor of an easier approach to [Custom Players](https://mirror-networking.com/docs/Articles/Guides/GameObjects/SpawnPlayerCustom.html).
  * This is a breaking change: The virtual method OnServerAddPlayer loses the AddPlayerMessage parameter.
* Changed: `NetworkIdentity.RemoveAuthorityForConnection` is now easier to use: no need to supply the current "owner" anymore.
* Changed: Renamed `NetworkConnection.playerController` to `identity` ... see [Deprecations](https://mirror-networking.com/docs/Articles/General/Deprecations.html) for details.
* Changed: Lobby system renamed to Room to better align the name for what it is and make way for a future Lobby framework.

## v3.17.4 - 2019-Sep-04 <a href="#version-3174---2019-sep-04" id="version-3174---2019-sep-04"></a>

* Added: Custom Network Readers & Writers via extension methods.
* Added: Network Sync Mode selector on components to sync to observers (default) or just the owner.
* Added: SyncVars now support structs and enums in other assemblies.
* Added: Support for reading and writing array segments.
* Added: Network Animator now has layers support.
* Added: New virtual method OnServerChangeScene to Network Manager.
* Added: XML summary comments for intellisense and future generated class docs.
* Updated Examples and Documentation.
* Fixed: SceneID was not set to 0 for prefab variants.
* Fixed: Observers were not properly rebuilt on scene changes.
* Fixed: SyncVar hooks were not able to change other SyncVars in Host mode.
* Fixed: Telepathy not setting socket options on accepted clients.
* Fixed: Catch IL2CPP bug.
* Fixed: Telepathy and Websockets now start connections ID's at 1 instead of 2.
* Fixed: Websockets support for SSL restored.
* Fixed: Network Manager no longer complains about missing player prefab if auto-create is disabled.
* Fixed: Removed a garbage allocation in Network Transform.
* Fixed: NetworkClient.Ready was being called unnecessarily in certain cases, e.g. SceneMessages.
* Changed: Documentation moved to website and API generated docs implemented.
* Changed: AddPlayerForConnection handler is now internal to keep safety checks intact.
* Changed: A bunch of messages converted to value types for performance.

## v3.11.6 - 2019-Jul-10 <a href="#version-3116---2019-jul-10" id="version-3116---2019-jul-10"></a>

* Fixed: Telepathy IPv4, IPv6, and FQDN should all work now.
* Fixed: TelepathyTransport error in UWP builds.
* Fixed: OnApplicationQuit is handled better now.
* Fixed: Performance and memory allocation improvements.
* Changed: Telepathy Source is now included instead of a DLL.

## v3.10.10 - 2019-Jun-19 <a href="#version-31010---2019-jun-19" id="version-31010---2019-jun-19"></a>

* Added: Scene Message now supports params for SceneMode (Normal / Additive) and PhysicsMode (2D / 3D).
* Added: ClientScene.Send now has an optional ChannelId parameter.
* Added: ASMDEF to Examples folder.
* Added: Support for sending and receiving ArraySegment
* Added: IReadOnlyList in SyncLists.
* Fixed: Network Manager not switching to correct scene in some cases.
* Fixed: ListServer Ping not found in WebGL.
* Fixed: TelepathyTransport.GetMaxPacketSize uses the new configurable max size.
* Fixed: Significant reduction in memory allocation and garbage collection.
* Changed: Use transform.localPosition and transform.localRotation for better VR support.
* Removed: Websockets send queues (they never worked) and SSL (temporarily because it didn't work).

## v3.6.7 -- 2019-Apr-28 <a href="#version-367----2019-apr-28" id="version-367----2019-apr-28"></a>

* Changed: NetworkReader now uses ArraySegment to minimize allocations.

## v3.6.6 -- 2019-Apr-24 <a href="#version-366----2019-apr-24" id="version-366----2019-apr-24"></a>

* Fixed: Reverted two internal refactor commits that had unintended consequences.

## v3.6.5 -- 2019-Apr-23 <a href="#version-365----2019-apr-23" id="version-365----2019-apr-23"></a>

* Fixed: Unity 2019.1 compatibility.
* Fixed: Erroneous error indicating prefabs were missing Scene ID's.
* Fixed: OnDeserializeSafely now works without allocations.
* Fixed: Weaver not writing symbol files, preventing breakpoints from working in Visual Studio.
* Fixed: Network Identity SceneID generation now uses RNG Crypto Service Provider.
* Fixed: Scene lighting in Additive example.
* Fixed: Player Prefab transform details now respected when instantiated in the absence of NetworkStartPosition.
* Removed: Tests folder from Unity package generation (no end-user value).

## v3.5.9 -- 2019-Apr-12 <a href="#version-359----2019-apr-12" id="version-359----2019-apr-12"></a>

* Fixed: Network Manager round-robin mode using NetworkStartPosition objects now uses hierarchy sibling order.
* Fixed: IsLocalPlayer is now reliably accurate in `Start()` by combining OwnerMessage and SpawnPrefabMessage.
* Fixed: Stack overflow issues with Weaver and Cecil.
* Fixed: Corrected assembly paths passed to weaver.
* Fixed: Enum bandwidth reduced in SyncVars, Commands, and Rpcs.
* Fixed: Placeholder files added for removed code files so updating works better.
* Changed: Network Manager `isHeadless` is a static property now, changed from `IsHeadless()`.

## v3.5.3 -- 2019-Apr-10 <a href="#version-353----2019-apr-10" id="version-353----2019-apr-10"></a>

* Fixed: Exceptions in overrides of Network Manager and other components incorrectly silenced.
* Fixed: Lobby system sometimes would not spawn and swap game player prefabs into the game scene.
* Fixed: Application.targetFrameRate no longer set in host mode.
* Changed: Telepathy: Split MaxMessageSize to allow setting a different value for client and server.

## v3.4.9 -- 2019-Apr-6 <a href="#version-349----2019-apr-6" id="version-349----2019-apr-6"></a>

* Added: Semantic Versioning (which is why we jumped from 1.6 to 3.4.9).
* Added: [SyncDictionary](https://mirror-networking.com/docs/Articles/Guides/Sync/SyncDictionary.html).
* Added: [SyncHashSet](https://mirror-networking.com/docs/Articles/Guides/Sync/SyncHashSet.html).
* Added: [SyncSortedSet](https://mirror-networking.com/docs/Articles/Guides/Sync/SyncSortedSet.html).
* Added: SyncList and SyncDictionary support all IList and IDictionary derived types, respectively.
* Added: Documentation for [SyncVars](https://mirror-networking.com/docs/Articles/Guides/Sync/SyncVars.html).
* Added: Documentation for [SyncEvents](https://mirror-networking.com/docs/Articles/Guides/Sync/SyncEvent.html).
* Added: NoRotation to Network Transform.
* Added: Scale is now included in spawn payload along with position and rotation.
* Added: Generic `IMessageBase` to allow struct message types.
* Added: Weaver now supports Vector2Int and Vector3Int.
* Added: List Server example.
* Added: Additive Scenes example.
* Fixed: SyncLists now work correctly for primitives and structs.
* Fixed: SyncVar Hooks now will update the local property value after the hook is called.
  * You no longer need to have a line of code in your hook method to manually update the local property.
* Fixed: Host should not call Disconnect on transports.
* Fixed: NetworkAnimimator now supports up to 64 animator parameters.
* Fixed: Network Manager `StartServer` no longer assumes scene zero is the default scene...uses `GetActiveScene` now.
* Fixed: `NetworkServer.Shutdown` now resets `netId` to zero.
* Fixed: Observers are now properly rebuilt when client joins and `OnRebuildObservers` / `OnCheckObserver` is overridden.
* Fixed: NetworkProximityChecker: On rare occasion, player could be excluded from observers rebuild.
* Fixed: NetworkLobbyPlayer `OnClientReady` works now.
* Fixed: NetworkLobbyPlayer Remove button not showing for P1 when Server Only.
* Fixed: NetworkLobbyManager `pendingPlayers` and `lobbySlots` lists are now public for inheritors.
* Fixed: Offline scene switching now works via `StopClient()`.
* Fixed: Pong example updated.
* Fixed: Source Weaver was deleting PDB files, preventing breakpoints and debugging from working.
* Changed: TargetRpc NetworkConnection parameter is now optional...the calling client's NetworkConnection is default.
* Changed: Movement example replaced with Tank example.
* Changed: NetworkClient functions are all static now, so the singleton is gone. Use NetworkClient directly.
* Changed: SyncList now supports structs directly, making SyncListSTRUCT obsolete.
* Removed: SyncListSTRUCT - Use SyncList instead.
* Removed: NetworkClient.ShutdownAll is obsolete -- Use NetworkClient.Shutdown instead.

## v1.6 -- 2019-Mar-14 <a href="#version-16----2019-mar-14" id="version-16----2019-mar-14"></a>

* Fixed: Websockets transport moved to Mirror.Websocket namespace.
* Fixed: Network Animator bandwidth abuse.
* Fixed: Network Animator float sync bug.
* Fixed: Persistent SceneID's for Networked objects.
* Changed: Documentation for [Transports](https://mirror-networking.com/docs/Articles/Transports/index.html).
* Changed: Weaver is now full source...FINALLY!
* Changed: ClientScene.AddPlayer 2nd parameter is now `byte[] extraData` instead of `MessageBase extraMessage`.
  * Please refer to the code sample [here](https://mirror-networking.com/docs/Articles/Guides/GameObjects/SpawnPlayerCustom.html) to see how to update your code.
* Changed: Network Manager -- Headless Auto-Start moved to `Start()` from `Awake()`.
* Changed: Removed Message ID's for all messages - See [Network Messages](https://mirror-networking.com/docs/Articles/Guides/Communications/NetworkMessages.html) for details.
  * Message IDs are now generated automatically based on the message name.
  * Previously you would call Send(MyMessage.MsgId, message), now you call Send(message).
* Removed: Documentation for Groove Transport - use Websockets Transport instead.

## v1.5 -- 2019-Mar-01 <a href="#version-15----2019-mar-01" id="version-15----2019-mar-01"></a>

* Added: **Migration Tool** to (mostly) automate conversion from UNet.
* Added: Full support for WebSockets and WebSocketsSecure to replace UNet LLAPI.
* Added: Transport Multiplexer - allows the use of multiple concurrent transports.
* Added: NetworkLobbyManager and NetworkLobbyPlayer with example game.
* Added: Configurable Server Tick rate in Network Manager.
* Added: New virtual OnClientChangeScene fires right before SceneManager.LoadSceneAsync is executed.
* Added: Unit tests for Weaver.
* Fixed: Garbage allocations removed from a lot of things (more work to do, we know).
* Fixed: NetworkProximityChecker now uses `OverlapSphereNonAlloc` and `OverlapCircleNonAlloc`.
* Fixed: SyncVar hook not firing when clients joined.
* Fixed: Network Manager no longer assumes it's on Scene(0) in Build Settings.
* Fixed: Network Animator no longer limited to 6 variables.
* Fixed: TelepathyTransport delivering messages when disabled.
* Changed: Minimum Unity version: **2018.3.6**
* Removed: SceneAttribute.cs (merged to CustomAttributes.cs).
* Removed: `NetworkClient.allClients` (Use `NetworkClient.singleton` instead).
* Removed: `NetworkServer.hostId` and `NetworkConnection.hostId` (holdovers from LLAPI).
* Removed: `NetworkConnection.isConnected` (Network Connection is always connected).
* Removed: `Transport.GetConnectionInfo` (Use `ServerGetClientAddress` instead).

## v1.4 -- 2019-Feb-01 <a href="#version-14----2019-feb-01" id="version-14----2019-feb-01"></a>

* Added: HelpURL attributes to components.
* Added: Automatic targetFramerate for headless builds.
* Added: ByteMessage to Messages class.
* Fixed: Connecting state can be canceled properly.
* Fixed: Network Transform interpolation applied to client's own object.
* Fixed: Objects are spawned with correct rotation.
* Fixed: SceneId assignment.
* Fixed: Changed syncInterval wasn't saved...it is now.
* Fixed: Additive Scene loading.
* Changed: **Mirror is now full source** -- no more DLL's.
* Changed: **Transports are now components** -- TCP, UDP, WebGL, Steam.
* Changed: Transport class now dispatches Unity Events.
* Changed: `NetworkServer.SendToClientOfPlayer` uses `NetworkIdentity` now.
* Changed: `NetworkServer.SendToObservers` uses `NetworkIdentity` parameter now.
* Changed: `NetworkServer.SendToReady` uses `NetworkIdentity` now.
* Changed: `NetworkServer.DestroyPlayerForConnection` uses `NetworkIdentity.spawned` now.
* Changed: `NetworkConnection.Dispose` uses `NetworkIdentity.spawned` now.
* Changed: `NetworkReader.ReadTransform` uses `NetworkIdentity.spawned` now.
* Changed: Network Transform reimplemented -- physics removed, code simplified.
* Removed: `NetworkClient.hostPort` (port is handled at Transport level).
* Removed: `NetworkServer.FindLocalObject` (Use `NetworkIdentity.spawned[netId]` instead).
* Removed: `ClientScene.FindLocalObject` (Use `NetworkIdentity.spawned[netId]` instead).
