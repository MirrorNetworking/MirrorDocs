# Getting Started

This document describes steps to creating a multiplayer game with Mirror. The process described here is a simplified, higher level version of the actual process for a real game; it doesn’t always work exactly like this, but it provides a basic recipe for the process.

## Video Tutorials <a href="#video-tutorials" id="video-tutorials"></a>

Check out these [awesome videos](../../community-guides/video-tutorials.md) showing you how to get started with mirror.

## Script Templates <a href="#script-templates" id="script-templates"></a>

* Create new Network Behaviours and other common scripts faster

See [Script Templates](script-templates.md).

## Network Manager Setup <a href="#networkmanager-set-up" id="networkmanager-set-up"></a>

* Create a new Network Manager from the [Assets > Create > Mirror](script-templates.md) menu.
* Add a new game object to the Scene and rename it “NetworkManager”.
* Add the newly created Network Manager component to the “NetworkManager” game object.
* Add the [NetworkManagerHUD](../components/network-manager-hud.md) component to the game object. This provides the default UI for managing the network game state.

See [Using the NetworkManager](../components/network-manager.md).

## Player Prefab <a href="#player-prefab" id="player-prefab"></a>

* Find the Prefab for the player game object in the game, or create a Prefab from the player game object
* Add the NetworkIdentity component to the player Prefab
* Set the `Player Prefab` in the NetworkManager’s Spawn Info section to the player Prefab
* Remove the player game object instance from the Scene if it exists in the Scene

See [Player Objects](../guides/gameobjects/player-gameobjects.md) for more information.

## Player Movement <a href="#player-movement" id="player-movement"></a>

* Add a NetworkTransform component to the player Prefab
* Check the Client Authority checkbox on the component.
* Update input and control scripts to respect `isLocalPlayer`
* Override OnStartLocalPlayer to take control of the Main Camera in the scene for the player.

For example, this script only processes input for the local player:

```csharp
using UnityEngine;
using Mirror;

public class Controls : NetworkBehaviour
{
    void Update()
    {
        // exit from update if this is not the local player
        if (!isLocalPlayer) return;

        // handle player input for movement
    }
}
```

## Basic player game state <a href="#basic-player-game-state" id="basic-player-game-state"></a>

* Make scripts that contain important data into Network Behaviours instead of MonoBehaviours
* Make important member variables into SyncVars

See [State Synchronization](../guides/synchronization/).

## Remote Actions <a href="#networked-actions" id="networked-actions"></a>

* Make scripts that perform important actions into Network Behaviours instead of MonoBehaviours
* Update functions that perform important player actions to be commands

See [Remote Actions](../guides/communications/remote-actions.md).

## Non-player GameObjects <a href="#non-player-game-objects" id="non-player-game-objects"></a>

Fix non-player prefabs such as enemies:

* Add the NetworkIdentity component
* Add the NetworkTransform component
* Register spawnable Prefabs with the NetworkManager
* Update scripts with game state and actions

## Spawners <a href="#spawners" id="spawners"></a>

* Potentially change spawner scripts to be NetworkBehaviours
* Modify spawners to only run on the server (use isServer property or the `OnStartServer()` function)
* Call `NetworkServer.Spawn()` for created game objects

## Spawn positions for players <a href="#spawn-positions-for-players" id="spawn-positions-for-players"></a>

* Add a new game object and place it at player’s start location
* Add the NetworkStartPosition component to the new game object
