---
description: Optimization for Lag Compensation & Client Side Prediction
---

# History Bounds

In the previous chapter, we discussed how to use lag compensation to check player's hitboxes in the past.

To minimize performance overhead, it can be useful to first cast against `HistoryBounds` before we decide which players to rollback.

&#x20;are essentially a 4-dimensional bounding box: (x,y,z,time).\
In other words, a bounding box which contains all the player's positions in the last second or so.



{% embed url="https://www.youtube.com/watch?v=LPfbAAIM3LY" %}
The orange Collider shows the Tank's red Collider over the last second.
{% endembed %}

### Usage guide:

* Attach the `HistoryCollider` component to your NetworkIdentity
* Make sure your NetworkIdentity has a collider, drag it into the component's `actualCollider` field
* Add a child GameObject with a BoxCollider and isTrigger enabled. Drag it into the component's `boundsCollider`.
* Press play, enable Gizmos, notice the orange HistoryCollider.
*   The component automatically adjusts the child trigger collider to match the orange box, which means that you can use Physics.Raycast against all the history colliders very easily.



In other words: if a player fires a gun, do this:

* Raycast against all HistoryColliders to find out which other player's we need to check
* Then apply lag compensation for the collider's parent NetworkIdentity
* Then check if it was hit

{% hint style="info" %}
This is a brief overview on why HistoryCollider exists, and how to use it.\
In the future we will provide a proper FPS demo with all the advanced techniques.
{% endhint %}
