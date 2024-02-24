---
description: Learn about Mirror's Lag Compensation for fast paced games.
---

# Lag Compensation

Lag Compensation (aka Rollback) is needed for fast pace / first person shooter games.

For MMORPGs, card games, round based games, strategy games, etc. this is not interesting.

## Why Lag Compensation

Let's look at the most common reason why games need lag compensation.

Consider a first person shooter like Counter-strike.

There are two players on the server: you, and your friend.

The server synchronizes your friend's position to you. It takes let's say **50 milliseconds**.

You fire at your friend by using a Mirror `[Command] CmdFire(Vector3 direction)`.

The `[Command]` takes another **50 ms** to reach the server.

The Server then checks if you hit your friend, and applies damage.

{% hint style="danger" %}
In the **100 ms** that passed, your friend has already moved two steps to the left.

As result, you'll always **miss the shot**.
{% endhint %}

<figure><img src="../../.gitbook/assets/image (71).png" alt=""><figcaption><p>Where you see your friend, and where your friend actually is. Image from <a href="https://developer.valvesoftware.com/wiki/Source_Multiplayer_Networking">Valve's Article</a>.</p></figcaption></figure>

This is why you need lag compensation for high paced / first person shooter games.

Otherwise players will always need to aim ahead of their targets.

{% hint style="info" %}
On paper, this doesn't sound like a big deal.\
In practice, players will immediately complain that they don't hit where they aim.

The difference between lag compensation turned on / off is very noticeable to everyone.
{% endhint %}

For a more in-depth understand, please read through [Valve's Source Multiplayer Networking article](https://developer.valvesoftware.com/wiki/Source\_Multiplayer\_Networking). It's a good read, and worth the time even if you don't understand all of it yet.

## Lag Compensation with Mirror

Our Lag Compensation is split into two parts.

#### LagCompensation.cs standalone Algorithm

First, there's the standalone, Unity independent,  C# LagCompensation.cs algorithm with full test coverage:

<figure><img src="../../.gitbook/assets/2023-07-05 - 14-26-07@2x.png" alt="" width="344"><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/2023-07-05 - 14-28-43@2x.png" alt=""><figcaption></figcaption></figure>

The algorithm can record and sample any History of type \<T>.\
In other words, you can custom fit it to your needs if you want to.\
Keep in mind that this is very low level, and it's easier to use the high level component instead!

#### Lag Compensator Component

The second part: our `Lag Compensator` component:

<figure><img src="../../.gitbook/assets/image (146).png" alt=""><figcaption></figcaption></figure>

This hides all the magic in one component which automatically records a history of Collider snapshots in 3D. Simply add this to your Player object, and it'll handle everything for you!

Next, find the place where your player is firing a weapon locally. There should be a \[Command] like CmdFireWeapon() that sends the input to the server. This is where instead of checking if a player hit another player, we first ask Lag Compensation for the other player's position at the time when the player shot it.

You can check our Shooter example to see this in action, it's as simple as:

```csharp
[Client]
void FireWeapon()
{
    // show muzzle flash, play sound etc. locally
    // ...
    
    // check if anything was hit
    if (Physics.Raycast(camera.position, camera.position + camera.forward, out RaycastHit hit))
    {
        Player target = hit.collider.GetComponent<Player>();
        
        // notify the server
        CmdFiredWeapon(target, camera.position, hit.position);
    }
}

[Command]
void CmdFiredWeapon(Player target, Vector3 originPoint, Vector3 hitPoint)
{
    // check ammo etc.
    // ...
    
    // 'this' fired at 'target'.
    // we need to grab 'target's LagCompensator to find out where it was.
    LagCompensator compensator = target.GetComponent<LagCompensator>();
    
    // next, we can use either of the compensator's 'Check...' functions.
    // viewer is the one who fired, so it's 'this' object.
    // Lag Compensator checks 'this' object's latency and then rewinds 'target's history by that time.
    if (compensator.RaycastCheck(this.connectionToClient, originPoint, hitPoint))
    {
         // valid hit!
    }
}
```

Note the above code is slightly simplified, but that's really all there's to it other than a few more optional configuration parameters in the LagCompensator's 'Check' functions.



{% hint style="info" %}
Our Lag Compensator is super easy to use, and you can still add more complex checks to if if needed. What matter is that our standalone LagCompensation.cs algorithm is generic \<T>, working with anything you need (2D, 3D, custom physics, etc.).
{% endhint %}

{% hint style="success" %}
Our Mirror Shooter demo uses the Lag Compensator.\
As of February 2024, it is almost complete and soon to be released!
{% endhint %}

#### Note: Algorithm Minimal Example

If you prefer to not use the convenience LagCompensator component, then you can check our 'Lag Compensation' demo in the Examples folder. It's super simple, not even networked, and shows only the algorithm without the component.

In other words: ignore this example if you are using the component!

<figure><img src="../../.gitbook/assets/2023-06-29 - lag compensation estimated time accurate by 6ms.png" alt=""><figcaption><p>Mirror's Lag Compensation Example</p></figcaption></figure>

**This demo is very easy to understand:**

* The filled **white square** is an object on the server
* The outlined white squares are the **captures** taken every interval.
* The **black square** is the object on the client, **lagging** behind by a few milliseconds
* You click the black square, which calls a `CmdFire()` on the server.
* If it highlights blue, you hit. If it highlights magenta, you didn't hit.
* The **black square** is exactly one square behind the **white square**, which means you'll only hit if it lag compensation functions properly.

This was Lag Compensation part one: the algorithm.

You can use this in games _today_, you just need to adapt the demo to your needs.

You also need to worry about rolling back the physics world, we don't have a demo for this _yet_.

