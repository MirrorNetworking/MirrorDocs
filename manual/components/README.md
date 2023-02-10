# Components

These core components are included in Mirror:

* [Network Animator](network-animator.md)\
  The Network Animator component allows you to synchronize animation states for networked objects. It synchronizes state and parameters from an Animator Controller.
* [Network Authenticator](network-authenticators/)\
  Network Authenticators facilitate integration of user accounts and credentials into your application.
* [Network Discovery](network-discovery.md)\
  Network Discovery uses a UDP broadcast on the LAN enabling clients to find the running server and connect to it.
* [Network Identity](network-identity.md)\
  The Network Identity component is at the heart of the Mirror networking high-level API. It controls a game object’s unique identity on the network, and it uses that identity to make the networking system aware of the game object. It offers two different options for configuration and they are mutually exclusive, which means either one of the options or none can be checked.
* [Network Manager](network-manager.md)\
  The Network Manager is a component for managing the networking aspects of a multiplayer game.
* [Network Manager HUD](network-manager-hud.md)\
  The Network Manager HUD is a quick-start tool to help you start building your multiplayer game straight away, without first having to build a user interface for game creation/connection/joining. It allows you to jump straight into your gameplay programming, and means you can build your own version of these controls later in your development schedule.
*   [Network Ping Display](network-ping-display.md)

    Network Ping Display shows the Ping time for clients using OnGUI
*   [Network Rigidbody](network-rigidbody.md)

    The Network Rigidbody synchronizes velocity and other properties of a rigidbody across the network.
*   [Network Lerp Rigidbody](network-lerp-rigidbody.md)

    The Network Lerp Rigidbody component synchronizes position and velocity of a rigidbody across the network.&#x20;
* [Network Room Manager](network-room-manager.md)\
  The Network Room Manager is an extension component of Network Manager that provides a basic functional room.
* [Network Room Player](network-room-player.md)\
  The Network Room Player is a component that's required on Player prefabs used in the Room Scene with the Network Room Manager above.
* [Network Start Position](network-start-position.md)\
  Network Start Position is used by the Network Manager when creating player objects. The position and rotation of the Network Start Position are used to place the newly created player object.
* [Network Statistics](network-statistics.md)\
  Shows Network messages and bytes sent & received per second via OnGUI.
* [Network Transform](network-transform/)\
  The Network Transform component synchronizes the movement and rotation of game objects across the network. Note that the network Transform component only synchronizes spawned networked game objects.
