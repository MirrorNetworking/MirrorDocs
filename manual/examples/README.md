# Examples

Mirror includes several small examples to help you learn how to use various features and how to set things up so they work together.\
Only a select few have documentation pages, check the Mirror/Examples directory in your Unity Project for more examples of how to use Mirror.

*   [Additive Levels](additive-levels.md)

    &#x20;The Additive Levels example demonstrates using Additive Scenes as levels with Scene Interest Management, custom scene loading with a fade transition, and teleporting the player from one scene to another via respawning.
* [Additive Scenes](additive-scenes.md)\
  &#x20;The Additive Scenes example demonstrates a server additively loading a sub-scene into a main scene at startup, and having a server-only trigger that generates a message to any client whose player enters the trigger zone to also load the sub-scene, and subsequently unload it when they leave the trigger zone. Only players inside the trigger zone can see the objects in the sub-scene. Network Proximity Checker components are key to making this scenario work.
* [Basic](basic.md)\
  &#x20;Basic is what it sounds like...the most rudimentary baseline of a networked game. Features SyncVars updating random UI data for each player.
* Benchmark\
  &#x20;Allows for mass testing of Enemy NPC's (monsterous red cubes!).\
  See the Spatial Hashing Interest Management component at work, joining clients will only receive data from nearby enemies.
* Benchmark Idle\
  &#x20;Similar to above, mass test idle networked objects each with a colour sync var, settings can be changed via NetworkManager inspector.
* Billiards\
  &#x20;Mouse drag the white ball to apply force, hits are send to server via \[Command].
* Billiards Predicted\
  &#x20;Similar to above, but with prediction, this solves the possible delay that a Command->ClientRpc/NetworkTransform position result, would normally have.
* CCU\
  &#x20;SoonTM&#x20;
* Character Selection\
  &#x20;Demonstrates how to use multiple prefabs for player spawns in your game, along with name and colour customisation.
* Chat\
  &#x20;A simple text chat example, send messages between players that login via Network Authenticator.
* Common\
  &#x20;Contains miscellaneous features that are not full examples.
* Couch Co-Op\
  &#x20;Add additional local players to your game, they all share same screen, different controls.\
  Remote joining clients can do the same, allows for a combination of local players vs remote players, such as 2 vs 4, or 1 vs 4 vs 99 (granted you would have to setup 99 different controller/keyboard inputs for this).
* Discovery\
  &#x20;Broadcasts on local network, clients can find servers without needing to input IP address or ports.\
  Another popular use is, it can be adjusted to auto join first broadcasted game thats found (or create a game if none), a feature used often in VR or LAN game types.&#x20;
*   Lag Compensation\
    &#x20;Rollback / Lag Compensation is a standalone, Unity / netcode independent algorithm. This is a simple demo to test it, without Mirror. We want this to be usable in all game engines.

    The demo intentionally introduces latency so that server / client cubes are at different positions when clicking.
* [Multiple Additive Scenes](multiple-additive-scenes.md)\
  &#x20;The Multiple Concurrent Additive Scenes example demonstrates a server additively loading multiple instances of a sub-scene into a main scene at startup, with physics separation, and assigning players to the instances to play matches independently. [NetworkSceneChecker](../components/deprecated/network-scene-checker.md) is the key component that makes this example work.
*   Multiple Matches\
    &#x20;This example demonstrates how to run a large number of non-physics games concurrently in a single game server instance.

    This would be most appropriate for Card, Board, Puzzle, and Arcade games where there is no physics involved, just presentation and messaging.

    While this example is turn-based, real-time games work just as well.
* [Pong](pong.md)\
  &#x20;A simple example for "How to build a multiplayer game with Mirror" is Pong. It illustrates the usage of `NetworkManager`, `NetworkManagerHUD`, NetworkBehaviour, NetworkIdentity, `NetworkTransform`, `NetworkStartPosition`and various Attributes.
* Rigidbody Benchmark\
  &#x20;Mass test networked physics.
* Rigidbody Physics\
  &#x20;Test differences between physics and network setups to find what is ideal for your scenario.
* [Room System](room.md)\
  &#x20;The Room System example demonstrates how to set up a "staging" scene where players assemble before starting a match. When all players are ready, the server sends them all a message to change scenes (along with the server itself) to the actual game play scene so they all come in at once. Includes fully playable game with a character controller where players collect server-spawned prizes for score.
* Snapshot Interpolation\
  &#x20;Snapshot Interpolation is a standalone, Unity / netcode independent algorithm. This is a simple demo to test it, without Mirror. We want this to be usable in all game engines.
* Sync Direction\
  &#x20;Sync List and Sync Var example, controlled via Client->Server sync direction.
* [Tanks](tanks.md)\
  &#x20;This is a simple scene with animated tanks, networked rigidbody projectiles, and NavMesh movement.
* Tanks Co-Op\
  &#x20;Take control of tank Vehicles, spawn as regular player prefab, interact with tank to enter, drive and shoot, exit to continue being regular player.
* VR\
  &#x20;A selection of multiplayer-ready virtual reality examples that use Unitys XR toolkit, allows the use of multiple VR headsets with one setup.&#x20;
