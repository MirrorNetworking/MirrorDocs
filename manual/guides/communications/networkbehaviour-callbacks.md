# NetworkBehaviour Callbacks

## NetworkBehaviour Callbacks <a href="#networkbehaviour-callbacks" id="networkbehaviour-callbacks"></a>

{% hint style="info" %}
See also [NetworkBehaviour](https://storage.googleapis.com/mirror-api-docs/html/db/d21/class\_mirror\_1\_1\_network\_behaviour.html) in the API Reference.
{% endhint %}

There are a number of events relating to network behaviours that can occur over the course of a normal multiplayer game. These include events such as the host starting up, a player joining, or a player leaving. Each of these possible events has an associated callback that you can implement in your own code to take action when the event occurs.

When you create a script which inherits from `NetworkBehaviour`, you can write your own implementation of what should happen when these events occur. To do this, you override the virtual methods on the `NetworkBehaviour` class with your own implementation of what should happen when the given event occurs.

This is a full list of virtual methods (callbacks) that you can implement on `NetworkBehaviour`, and where they are called

### Server Only <a href="#server-only" id="server-only"></a>

* OnStartServer
  * called when behaviour is spawned on server
* OnStopServer
  * called when behaviour is destroyed or unspawned on server
* OnSerialize
  * called when behaviour is serialize before it is sent to client, when overriding make sure to call `base.OnSerialize`

### Client only <a href="#client-only" id="client-only"></a>

* OnStartClient
  * called when behaviour is spawned on client
* OnStartAuthority
  * called when behaviour has authority when it is spawned (eg local player)
  * called when behaviour is given authority by the sever
* OnStartLocalPlayer
  * called when the behaviour is on the local player object
* OnStopAuthority
  * called when authority is taken from the object (eg local player is replaced but not destroyed)
* OnStopClient
  * called when object is destroyed on client by the `ObjectDestroyMessage` or `ObjectHideMessage` messages

## Example flows <a href="#example-flows" id="example-flows"></a>

Below is some example call order for different modes

> NOTE: `Start` is called by unity before the first frame, while normally this happens after Mirror's callbacks. But if you dont call `NetworkServer.Spawn` the same frame as `instantiate` then start may be called first

> Note: `OnRebuildObservers` and `OnSetHostVisibility` is now on `NetworkVisibility` instead of `NetworkBehaviour`

### Server mode <a href="#server-mode" id="server-mode"></a>

When a NetworkServer.Spawn is called (eg when new client connections and a player is created)

* `OnStartServer`
* `OnRebuildObservers`
* `Start`

### Client mode <a href="#client-mode" id="client-mode"></a>

When local player is spawned for client

* `OnStartAuthority`
* `OnStartClient`
* `OnStartLocalPlayer`
* `Start`

### Host mode <a href="#host-mode" id="host-mode"></a>

These are only called on the **Player Game Objects** when a client connects:

* `OnStartServer`
* `OnRebuildObservers`
* `OnStartAuthority`
* `OnStartClient`
* `OnSetHostVisibility`
* `OnStartLocalPlayer`
* `Start`
