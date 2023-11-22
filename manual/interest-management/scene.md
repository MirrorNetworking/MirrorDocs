---
description: Scene Interest Management
---

# Scene

## Scene Interest Management

Scene Interest Management is used with Additive Scenes to networked objects to subscenes with physics isolation.  That means that even if you have several instances of the same subscene loaded on the server, collisions and such between objects only happen within that subscene without interfering with others.

{% hint style="info" %}
Server and connected clients always have the same main scene loaded. With additive scenes, this is typically a container scene, and additive subscenes have the actual scene contents.
{% endhint %}

### Setting Up

Add the **Scene Interest Management** component to the same object as your **Network Manager**:

![](<../../.gitbook/assets/image (94).png>)

### Add Physics Simulator

In each subscene, add an empty game object, and add a **Physics Simulator** component to that.  Since additive scenes will be loaded on the server as "[physics scenes](https://docs.unity3d.com/ScriptReference/PhysicsScene.html)" Unity doesn't simulate physics for them, so this component does that for you in each subscene.

![](<../../.gitbook/assets/image (24).png>)

### Environment Content

Also in each subscene, create an empty game object called **Environment** with a **Network Identity** and make all static non-networked content to be children of this object.  Typically this would include scenery, such as buildings, road meshes and other non-interactive content.

![](<../../.gitbook/assets/image (15).png>)

{% hint style="warning" %}
Do not put anything under the Environment object that will be networked and/or may be interactive by players, e.g. doors, pickups, kiosks, etc.
{% endhint %}

### Spawning Players

When you instantiate a player or other networked object, Unity doesn't have any built-in mechanism to specify which subscene the object is instantiated to...it's always the active scene, which is our container scene.  After instantiating and before spawning the object, call `SceneManager.MoveGameObjectToScene` to move the object into the correct subscene. Once it's spawned it will be visible to clients with player objects in the same subscene.  If you move it to another subscene, visibility in both the new and previous subscenes will be updated automatically.

{% hint style="info" %}
Note that the networked objects are only moved to subscenes on the server.  Clients spawn everything in the container scene, and there's no need to move objects to a subscene on the clients, since they only have one subscene loaded at a time.
{% endhint %}

### Raycasting and Such

Physics scenes have a special set of methods for Raycast and such that must be used on the server since the server has the subscenes as physics scenes.  You can read about those [here](https://docs.unity3d.com/ScriptReference/PhysicsScene.html).  On the remote (non-Host) clients, raycasting is done with the normal methods, since the client just merges the additive scene with the container without it being a physics scene.
