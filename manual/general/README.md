# General

Mirror is a system for building multiplayer capabilities for Unity games. It is built on top of the lower level transport real-time communication layer, and handles many of the common tasks that are required for multiplayer games. While the transport layer supports any kind of network topology, Mirror is a server authoritative system; although it allows one of the participants to be a client and the server at the same time, so no dedicated server process is required. Working in conjunction with the internet services, this allows multiplayer games to be played over the internet with little work from developers.

Mirror is focused on ease of use and iterative development and provides useful functionality for multiplayer games, such as:

* Message handlers
* General purpose high performance serialization
* Distributed object management
* State synchronization
* Network classes: Server, Client, Connection, etc

Mirror is built from a series of layers that add functionality:

![](<../../.gitbook/assets/image (111).png>)

## Server and Host <a href="#server-and-host" id="server-and-host"></a>

Mirror multiplayer games include:

* **Server**\
  &#x20;A server is an instance of the game which all other players connect to when they want to play together. A server often manages various aspects of the game, such as keeping score, and transmit that data back to the client.
* **Clients**\
  &#x20;Clients are instances of the game that usually connect from different computers to the server. Clients can connect over a local network, or online.

A client is an instance of the game that connects to the server, so that the person playing it can play the game with other people, who connect on their own clients.

The server might be either a “dedicated server”, or a “host server”.

* **Dedicated server**\
  &#x20;This is an instance of the game that only runs to act as a server.
* **Host server**\
  &#x20;When there is no dedicated server, one of the clients also plays the role of the server. This client is the “host server”. The host server creates a single instance of the game (called the host), which acts as both server and client.

The diagram below represents three players in a multiplayer game. In this game, one client is also acting as host, which means the client itself is the “local client”. The local client connects to the host server, and both run on the same computer. The other two players are remote clients - that is, they are on different computers, connected to the host server.

![](<../../.gitbook/assets/image (86).png>)

The host is a single instance of your game, acting as both server and client at the same time. The host uses a special kind of internal client for local client communication, while other clients are remote clients. The local client communicates with the server through direct function calls and message queues, because it is in the same process. It actually shares the Scene with the server. Remote clients communicate with the server over a regular network connection. When you use Mirror’s, this is all handled automatically for you.

One of the aims of the multiplayer system is for the code for local clients and remote clients to be the same, so that you only have to think about one type of client most of the time when developing your game. In most cases, Mirror handles this difference automatically, so you should rarely need to think about the difference between your code running on a local client or a remote client.

## Instantiate and Spawn <a href="#instantiate-and-spawn" id="instantiate-and-spawn"></a>

When you make a single player game In Unity, you usually use the `GameObject.Instantiate` method to create new game objects at runtime. However, with a multiplayer system, the server itself must “spawn” game objects in order for them to be active within the networked game. When the server spawns game objects, it triggers the creation of game objects on connected clients. The spawning system manages the lifecycle of the game object, and synchronizes the state of the game object based on how you set the game object up.

For more details about networked instantiating and spawning, see documentation on Spawning [GameObjects](../guides/gameobjects/).

## Players and Local Players <a href="#players-and-local-players" id="players-and-local-players"></a>

Mirror handles player game objects differently to non-player game objects. When a new player joins the game (when a new client connects to the server), that player’s game object becomes a “local player” game object on the client of that player, and Mirror associates the player’s connection with the player’s game object. Mirror associates one player game object for each person playing the game, and routes networking commands to that individual game object. A player cannot invoke a command on another player’s game object, only their own.

For more details, see documentation on Player [GameObjects](../guides/gameobjects/).

## Authority

Servers and clients can both manage a game object’s behavior. The concept of “authority” refers to how and where a game object is managed. Mirror is based around “server authority” as the default state, where the Server has authority over all game objects. Player game objects are a special case and treated as having “local authority”. You may want to build your game using a different system of authority - for more details, see [Network Authority](../guides/authority.md).
