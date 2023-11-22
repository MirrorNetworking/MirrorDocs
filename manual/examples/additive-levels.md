# Additive Levels

The Additive Levels example demonstrates the following:

* Using Additive Scenes with Scene Interest Management.
* Teleporting between levels via portals and respawning.
* Custom Scene Loading with Fade In / Out transition.

## Setting Up

For this example to work correctly, some setup is required.

First, add all the scenes to Build Settings:

<div align="left">

<img src="../../.gitbook/assets/image (128).png" alt="Build Settings">

</div>

Next, add a Player and Portal Layer to Project Settings under Tags and Layers:

<img src="../../.gitbook/assets/image (74).png" alt="" data-size="original">

... and in Physics settings set the matrix so that Portal only collides with Player:

<img src="../../.gitbook/assets/image (112).png" alt="" data-size="original">

Finally, open the Offline scene, and click Play in the editor, and click Host in the HUD.

* Player controls are WASD+QE to move and turn.
* Move into the Portal in the corner with a scene label over it.  Your player will be changed to the other level scene.
* Move into the Portal in the corner of the 2nd scene, and your player will be changed back to the first scene

If you make a build and start two or more instances (one as Host or Server, the other(s) as clients, and move each one, you'll see that only players in the same level can see and collide with each other.
