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

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption><p>Where you see your friend, and where your friend actually is. Image from <a href="https://developer.valvesoftware.com/wiki/Source_Multiplayer_Networking">Valve's Article</a>.</p></figcaption></figure>

This is why you need lag compensation for high paced / first person shooter games.

Otherwise players will always need to aim ahead of their targets.

{% hint style="info" %}
On paper, this doesn't sound like a big deal.\
In practice, players will immediately complain that they don't hit where they aim.

The difference between lag compensation turned on / off is very noticeable to everyone.
{% endhint %}

For a more in-depth understand, please read through [Valve's Source Multiplayer Networking article](https://developer.valvesoftware.com/wiki/Source\_Multiplayer\_Networking). It's a good read, and worth the time even if you don't understand all of it yet.

## Mirror's Lag Compensation Algorithm

We implemented Lag Compensation as a standalone C# algorithm, independent of Unity and Mirror. As result, it's very easy to reason about, modify, test and experiment with. It can be used on any data / component by implementing the `Capture` interface and calling the `LagCompensation<T: Capture>` algorithms.

The algorithm can be found in `Mirror/Core/LagCompensation.cs`:

<figure><img src="../../.gitbook/assets/2023-07-05 - 14-26-07@2x.png" alt="" width="344"><figcaption></figcaption></figure>

To ensure stability, it comes with full test coverage around all the edge cases:

<figure><img src="../../.gitbook/assets/2023-07-05 - 14-28-43@2x.png" alt=""><figcaption></figcaption></figure>

Here is a basic overview on how to use it:

* Create a `Capture` of the player or world state every interval. For example, you may have a struct `PlayerCapture : Capture`which contains the player's position and rotation at that time.
* Define a `Queue<KeyValuePair<time, capture>>`.
* Every capture interval, call `LagCompensation.Insert<T>()` to insert a new capture. The Insert function automatically removes older captures beyond the size limit, and takes care of all the edge cases for you.
* In your `CmdFire()` function, you need to:
  * Estimate how far behind the client is. We don't want to trust the client to tell us that time. Instead, use `LagCompensation.EstimateTime()` for a server authoritative estimation.
  * Then call `LagCompensation.Sample(buffer, time)` to get the capture at that time. Note that your estimated time is most certainly between two captures. That's why it always returns '`before`' and '`after`', which you can then use to interpolate between to find the player's position at that exact time.
  * Now that you have the interpolated capture, you can check if it was where the player fired at. For example, you may want to use simplified raycasts, or rollback the physics engine.

{% hint style="info" %}
Traditionally, Lag Compensation algorithms would pick either the '**before**' or '**after**' result from the Sample() function. If your performance budget allows for it, feel free to **Interpolate(before, after, t)** to get a more precise capture to test against. This is commonly referred to as "**sub-tick**" interpolation (see our demo).
{% endhint %}

To summarize, you need to: `Capture`, `Insert`, `EstimateTime`, `Sample`.

We intentionally kept the implementation as simple as possible, while giving you full freedom about the details about what you capture, how you interpolate, and how you check the sample's collision.

For a minimal example, please try our Lag Compensation Example in the Examples folder:

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

## Demo: Shooter with Lag Compensation

{% hint style="info" %}
We wanted to ship the Lag Compensation algorithm as early as possible.

A complete **FPS game demo** is on our [roadmap](https://discord.com/channels/343440455738064897/468084877338869791/756431342627061770) for later this year!

**For now,** please read the Valve article, check our algorithm and try the simple demo!
{% endhint %}
