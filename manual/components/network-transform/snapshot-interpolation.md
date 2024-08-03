# Snapshot Interpolation

Mirror's `NetworkTransform` component uses [**Snapshot Interpolation**](https://gafferongames.com/post/snapshot\_interpolation/).

{% hint style="info" %}
If you are not familiar with the term: **Snapshot Interpolation** interpolates through snapshots, while buffering enough of them to make up for non-ideal networking conditions like latency, packet loss and scramble.
{% endhint %}

When developing the new `NetworkTransform` component, we've had two goals:

* **Make it stable**: We needed something that can be used by thousands of projects without _any_ surprises. Major hits already use Mirror in production, so we needed to be extremely careful to get this right.
* **Make it reusable**: NetworkTransform is one of many components that need Snapshot Interpolation. Someone might need it for 3D / 2D Rigidbodies, Character Controllers and more. The algorithm however is always the same.

In order to achieve both goals, we decided to split the **Snapshot Interpolation** algorithm into a standalone class that can be used by anyone. It's raw C#, completely independent from Mirror & Unity. You can use it in Mirror, or in standalone servers or in different game engines.

In short, we wanted to **solve the problem** once and for all, so we have an algorithm that we can use for decades, no matter which engine.

{% hint style="info" %}
The approach is comparable to **kcp**, which is merely a reliability algorithm that can be used in all languages & engines. For our kcp transport, we simply wrap it in server & client classes and send the results over UDP sockets.
{% endhint %}

## Simulations & Tests

**Snapshot Interpolation** is quite difficult to get right. Not only do we operate on two timelines (local & remote), we also have to deal with adverse network conditions like latency spikes, packet loss and scramble. To make matters worse, servers & clients might also be under heavy load and update on significantly different frequencies at times.

{% hint style="info" %}
To put things into perspective: the Snapshot Interpolation algorithm took us **4 months** of work to get it to work. More than half the time was spent on tests & simulations to guarantee stability.
{% endhint %}

Developing Snapshot Interpolation as a standalone algorithm allows us to **simulate** the different scenarios without even running latency simulation or Mirror:

* We can simulate exactly how the snapshot interpolation algorithm behaves if the remote is at t=5, we are at t=3 and we have three snapshots at t=0, 1, 2, while **sampling** through different points of the interpolation.&#x20;
* We can simulate what happens if someone only has two snapshots, reached the end of the interpolation and is still **waiting** for the next one.
* We can simulate how the algorithm behaves if a mobile user switches back to the game after a **100s delay**.
* We can simulate extremely poor network conditions like 99% **packet loss & scramble**.
* We can **catch-up** if the buffer gets too large.
* And many more..

![Snapshot Interpolation Simulations](../../../.gitbook/assets/2021-07-06\_20-23-58@2x.png)

**As result**

_It's only a matter of computing with snapshots & parameters in, result out._intended

## Using the Algorithm

Please read through our `SnapshotInterpolation.cs` code in Mirror to see the algorithm & helper functions, and then read through `NetworkTransform` as usage example.

To summarize, there are a few key aspects:

* The **Snapshot** **interface**: your `RigidbodySnapshot`, `CharacterControllerSnapshot` etc. structs have to implement that interface.
* The **Time Buffer**, which is a `SortedList<timestamp, Snapshot>` buffer. We offer several helper functions like `InsertIfNewEnough` for convenience.&#x20;
  * All of those functions are heavily covered with unit tests.
* The **Compute()** algorithm: given a snapshot buffer, time, deltaTime and configuration parameters, it spits out the next interpolated Snapshot (if any).&#x20;
  * This function comes with heavy test coverage, in fact it's likely the most tested function in all of Mirror.

{% hint style="success" %}
Make sure to read the [Snapshot Interpolation](https://gafferongames.com/post/snapshot\_interpolation/) article to understand how it all works, and then look through`SnapshotInterpolation.cs` and `NetworkTransformBase.cs`to see it in action. Even with our provided algorithm, it's still not an easy topic to understand and implement correctly.
{% endhint %}

{% hint style="warning" %}
Note how`NetworkTransform` sends snapshots **every**`sendInterval`over the **unreliable** channel. Do not send **only if changed**, this would require knowledge about the other end's last received snapshot (either over **reliable**, or with a **notify** algorithm).
{% endhint %}

{% hint style="info" %}
Note that NetworkTransform sends **every**`sendInterval`. Bandwidth will be significantly reduced once we implement **Bitpacking** and **Delta Compression** into Mirror.
{% endhint %}

## Benchmarks & Results

We recommend using Mirror's **LatencySimulationTransport** to try it yourself, for example with our **Benchmark** demo. Even for poor networking conditions, Snapshot Interpolation will perform well as long as the **bufferMultiplier** is high enough.

#### Some test videos:

* NetworkTransform [old vs. new comparison](https://youtu.be/z2JpT\_qLmzk) on Youtube (secret project).
* See the original [Pull Request](https://github.com/vis2k/Mirror/pull/2791) progress videos. You can see how latency, loss & scramble are gradually solved with every added feature.
* JesusLovsYooh old vs. new NetworkTransform _(watch the left build, OG NT is the old one and NT 2k is the new one)_

{% embed url="https://www.youtube.com/watch?v=fu7w9vlG7yQ" %}
Skip halfway through to see new NT under bad network conditions.
{% endembed %}
