# Network Behaviour

{% hint style="info" %}
See also [NetworkBehaviour](https://storage.googleapis.com/mirror-api-docs/html/db/d21/class\_mirror\_1\_1\_network\_behaviour.html) in the API Reference.
{% endhint %}

Network Behaviour scripts work with game objects that have a Network Identity component. These scripts can perform high-level API functions such as Commands, ClientRpc's, and SyncVars.

{% hint style="danger" %}
Do not put objects in `DontDestroyOnLoad` (DDOL) in Awake.\
You can do that in Start instead.
{% endhint %}

With the server-authoritative system of Mirror, the server must use the `NetworkServer.Spawn` function to spawn game objects with Network Identity components. Spawning them this way assigns them a `netId` and creates them on clients connected to the server.

**Note:** This is not a component that you can add to a game object directly. Instead, you must create a script which inherits from `NetworkBehaviour` (instead of the default `MonoBehaviour`), then you can add your script as a component to a game object.

## Properties <a href="#properties" id="properties"></a>

* **isServer**\
  Returns true in server context if this game object has been spawned.
* **isClient**\
  Returns true in client context if this game object has been spawned by the server.
* **isLocalPlayer**\
  Returns true on the client if this game object represents the player created for this client.
* **isOwned** (formerly `hasAuthority`)\
  Returns true on the client if this client has [authority](../guides/authority.md) over this game object. It is meaningless in server context.
* **netId**\
  The unique network ID of this game object. The server assigns this at run time. It is unique for all game objects on the network.
* **netIdentity**\
  Returns the Network Identity of this object
* **connectionToServer**\
  The Network Connection associated with the Network Identity component attached to this game object. This is only valid for the **local player object** on the client, and is null for other player objects that may exist on the client.
* **connectionToClient**\
  The Network Connection associated with the Network Identity component attached to this game object. This is valid for game objects on the server that have been assigned to a specific client, e.g. player objects, pets, henchmen, or other objects a single client "owns".

Network Behaviour scripts have the following features:

* Synchronized variables
* Network callbacks
* Server and client functions
* Sending commands
* Client RPC calls
* Networked events

![](<../../.gitbook/assets/image (96).png>)

## Network Callbacks <a href="#network-callbacks" id="network-callbacks"></a>

There are built-in callback functions which are invoked on Network Behaviour scripts for various network events. These are virtual functions on the base class, so you can override them in your own code like this:

```csharp
public class SpaceShip : NetworkBehaviour
{
    public override void OnStartServer()
    {
        // disable client stuff
    }

    public override void OnStartClient()
    {
        // register client events, enable effects
    }
}
```

The built-in callbacks are:

* **OnStartServer** called on server when a game object spawns on the server, or when the server is started for game objects in the Scene
* **OnStopServer** called on server when a game object is destroyed on the server, or when the server is stopped for game objects in the Scene
* **OnStartClient** called on clients when the game object spawns on the client, or when the client connects to a server for game objects in the Scene
* **OnStopClient** called on clients when the server destroys the game object
* **OnStartLocalPlayer** called on clients after `OnStartClient` for the player game object on the local client
* **OnStopLocalPlayer** called on clients before `OnStopClient` for the player game object on the local client
* **OnStartAuthority** called on owner client when assigned authority by the server. `isOwned` will be true for such objects in client context.
* **OnStopAuthority** called on owner client when authority is removed by the server.

Note that in a peer-hosted setup, when one of the clients is acting as both host and client, both `OnStartServer` and `OnStartClient` are called on the same game object. Both these functions are useful for actions that are specific to either the client or server, such as suppressing effects on a server, or setting up client-side events.

## Server and Client Attributes <a href="#server-and-client-functions" id="server-and-client-functions"></a>

You can tag methods in Monobehaviour and Network Behaviour scripts with custom attributes to designate them as server-only or client-only functions. `[Server]` and `[ServerCallback]` return immediately if the client is not active. Likewise, `[Client]` and `[ClientCallback]` return immediately if the server is not active.

The `[Server]` and `[Client]` attributes do not generate compile time errors, but they do emit a warning log message if called in the wrong context.

The `ServerCallback` and `ClientCallback` attributes act the same as above but do not cause a warning to be generated.

For more information, see [Attributes](../guides/attributes.md).

## Commands <a href="#commands" id="commands"></a>

To execute code on the server, you must use Commands. The high-level API is a server-authoritative system, so Commands are the only way for a client to trigger some code on the server.

Typically only player objects can have Commands, however they may also exist on other networked objects, and may be called by a client that has authority assigned for that object, or by any client where [bypassing authority](../guides/communications/remote-actions.md) has been specifically indicated.

When a client player game object sends a command, that command runs on the corresponding player game object on the server. This routing happens automatically, so it is impossible for a client to send a command for a different player.

To define a Command in your code, you must decorate a method with the \[Command] attribute. While not required, it is strongly suggested to prefix method names with `Cmd` so it's easier to recognize in your calling code what will be happening.

Commands are called just by invoking the method normally on the client. Instead of the method running on the client, it is automatically invoked on the corresponding player game object on the server.

Commands are type-safe, have built-in security and routing to the player, and use an efficient serialization mechanism for the arguments to make calling them fast.

See [Communications](../guides/communications/) and related sections for more information.

## Client RPC Calls <a href="#client-rpc-calls" id="client-rpc-calls"></a>

Client RPC calls are a way for server game objects to make things happen on client game objects.

Client RPC calls are not restricted to player game objects, and may be called on any game object with a Network Identity component.

To define a Client RPC call in your code, you must decorate the method with `[ClientRpc]`. While not required, it is strongly suggested to prefix method names with `Rpc` so it's easier to recognize in your calling code what will be happening.

See [Communications](../guides/communications/) and related sections for more information.

## Networked Events (Obsolete) <a href="#networked-events-obsolete" id="networked-events-obsolete"></a>

> **IMPORTANT** SyncEvents have been removed in version 18.0.0, see this [Issue](https://github.com/vis2k/Mirror/pull/2178) for more information

Networked events are like Client RPC calls, but instead of calling a function on the game object, they trigger Events instead.

This allows you to write scripts which can register for a callback when an event is triggered.

To define a Networked event in your code, you must write a function which both:

* Has a name that begins with `Event`
* Has the `SyncEvent` attribute

You can use events to build powerful networked game systems that can be extended by other scripts. This example shows how an effect script on the client can respond to events generated by a combat script on the server.

SyncEvent is the base class that Commands and ClientRpc calls are derived from. You can use the SyncEvent attribute on your own functions to make your own event-driven networked game play code. Using SyncEvent, you can extend Mirror’s Multiplayer features to better fit your own programming patterns.

See [SyncEvents](../guides/synchronization/syncevent.md) for more details.
