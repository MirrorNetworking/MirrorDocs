# Guides

## Networking Concepts Overview

### High Level Scripting API <a href="#high-level-scripting-api" id="high-level-scripting-api"></a>

Mirror is a “high-level” networking library. Using this means you get access to commands which cover most of the common requirements for multiuser games without needing to worry about the “lower level” implementation details. Mirror allows you to:

* Control the networked state of the game using a “Network Manager”.
* Operate “client hosted” games, where the host is also a player client.
* Serialize data using a general-purpose serializer.
* Send and receive network messages.
* Send networked commands from clients to servers.
* Make remote procedure calls (RPCs) from servers to clients.
* Send networked events from servers to clients.

### Low Level Transport API

Mirror requires a low level [Transport](../transports/) to **connect/disconnect/send/recv** messages on a byte\[] level.

### Engine and Editor integration <a href="#engine-and-editor-integration" id="engine-and-editor-integration"></a>

Mirror’s networking is integrated into the engine and the editor, allowing you to work with components and visual aids to build your multiplayer game. It provides:

* A NetworkIdentity component for networked objects.
* A NetworkBehaviour for networked scripts.
* Configurable automatic synchronization of object transforms.
* Automatic synchronization of script variables.
* Support for placing networked objects in Unity scenes.
* Network components
