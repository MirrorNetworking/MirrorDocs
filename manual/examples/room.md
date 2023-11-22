---
description: Room example that demonstrates Mirror's built-in Room system.
---

# Room

{% hint style="success" %}
The hotel staff gets annoyed if you party in the Lobby, they'll tell you to get a **Room**!
{% endhint %}

Mirror's Room system is intended to be the "next step" after a separate Lobby service, typically a master server with match making and game server launching features.

{% hint style="warning" %}
Note that Mirror's Room system is not designed to allow late joiners / spectators once the game has started.  If you need these features, you're better off making your game based on Additive Scenes instead.
{% endhint %}

## Initial Setup

The typical setup involves 3 scenes:

* Offline scene, generally where a Lobby might be shown from a separate service
* Room scene, generally where players of a game instance assemble and ready-up before play starts
* Game scene, where the game play actually takes place

{% hint style="info" %}
To make your own game with the Room system, we have [Script Templates](../general/script-templates.md) to get you started.
{% endhint %}

1. Create 3 empty scenes: Offline, Room, Game.  You can use your own game scene for Game scene if you already have one.
2. In both the Offline and Room scenes, delete the Directional Light, and change the Clear Flags setting in the Main Camera to Solid Color (to make the default UI easier to see).
3. From the Script Templates, create your own Network Room Manager and Network Room Player scripts.
4. In the Offline scene, create an empty game object called `RoomPlayer` and attach the Network Room Player script you just created, then make a prefab of this object and delete it from the scene.
5. In the Offline scene, create an empty game object called `RoomManager`, and add the Network Room Manager script you just created, and a Network Manager HUD component from the Add Component menu.
6. In the Network Room Manager inspector, assign the following:
   * Offline scene to the Offline Scene field
   * Room scene to **both** the Online Scene and Room Scene fields
   * Game scene to the Gameplay scene field
   * `RoomPlayer` prefab to the Room Player Prefab field
   * Your own player prefab to the Player Prefab field
7. Compare your assignments and settings with the inspectors shown in the images below.
8. Add all 3 scenes to Build Settings with Offline scene being first, and build the project.
9. With the Offline scene open, click Play, and then Host button in the HUD
10. Start one or more built clients and click Client in the HUD for each one.
11. When all clients have clicked their respective Ready buttons, the Game scene will be loaded for everyone.
12. In the Game scene, the Host client will see a Return to Room button that will take everyone back to the Room scene to start another game with the same players.  Room Player objects do nothing in the Game scene, but are not destroyed so that when returning to the Room scene they'll be intact.

## Next Steps

Review the stubbed out overrides in the Network Room Manager and Network Room Player scripts you created from the templates.  They're heavily commented on what they're to be used for.

You can turn off the default UI in the Network Room Manager and Network Room Player inspectors by unchecking the Show Room UI checkbox, and then make your own UI for the Room scene.

* Suggest looking at how the [Basic example](basic.md) is done for the Player UI to see how to make a scene canvas and inject locally instantiated UI parts for each player.

![](<../../.gitbook/assets/image (121).png>)

![](<../../.gitbook/assets/image (30).png>)

![](<../../.gitbook/assets/image (38).png>)

![](<../../.gitbook/assets/image (67).png>)
