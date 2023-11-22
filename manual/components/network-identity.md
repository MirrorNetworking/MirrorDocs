# Network Identity

{% hint style="info" %}
See also [NetworkIdentity](https://storage.googleapis.com/mirror-api-docs/html/d3/d88/class\_mirror\_1\_1\_network\_identity.html) in the API Reference.
{% endhint %}

The Network Identity component is at the heart of the Unity networking high-level API. It controls a game object’s unique identity on the network, and it uses that identity to make the networking system aware of the game object. It offers two different options for configuration and they are mutually exclusive, which means either one of the options or none can be checked.

* **Server Only**\
  &#x20;Tick this checkbox to ensure that Unity only spawns the game object on the server, and not on clients.

![](<../../.gitbook/assets/image (50).png>)

{% hint style="danger" %}
It is important to note that Mirror does not support Network Identities on nested GameObjects. Otherwise, Mirror will emit an error. To avoid this, ensure your parent GameObject is the only GameObject in the stack with a Network Identity. Child GameObjects can access the parents' Network Identity component via Unity's built-in GetComponentInParent scripting function.
{% endhint %}

## Instantiated Network Game Objects <a href="#instantiated-network-game-objects" id="instantiated-network-game-objects"></a>

With the Mirror’s server-authoritative networking system, the server must spawn networked game objects with network identities, using `NetworkServer.Spawn`. This automatically creates them on clients that are connected to the server, and assigns them a `netId`.

You must put a Network Identity component on any Prefabs that spawn at runtime for the network system to use them.

## Scene-based Network Game Objects <a href="#scene-based-network-game-objects" id="scene-based-network-game-objects"></a>

You can also network game objects that are saved as part of your Scene (for example, environmental props). Networking game objects makes them behave slightly differently, because you need to have them spawn across the network.

When building your game, Unity disables all Scene-based game objects with Network Identity components. When a client connects to the server, the server sends spawn messages to tell the client which Scene game objects to enable and what their most up-to-date state information is. This ensures the client’s game does not contain game objects at incorrect locations when they start playing, or that Unity does not spawn and immediately destroy game objects on connection (for example, if an event removed the game object before that client connected). See [Networked Scene Game Objects](../guides/gameobjects/scene-gameobjects.md) for more information.

## Preview Pane Information <a href="#preview-pane-information" id="preview-pane-information"></a>

This component contains network tracking information, and displays that information in the preview pane. For example, the scene ID, network ID and asset ID the object has been assigned. This allows you to inspect the information which can be useful for investigation and debugging.

![](<../../.gitbook/assets/image (132).png>)

At runtime there is more information to display here (a disabled NetworkBehaviour is displayed non-bold):

![](<../../.gitbook/assets/image (117).png>)
