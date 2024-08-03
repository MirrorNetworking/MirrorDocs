# Network Transform

The Network Transform component synchronizes the position, rotation, and scale of networked game objects across the network.

<div align="center">

<figure><img src="../../../.gitbook/assets/image (63).png" alt=""><figcaption><p>Network Transform Component</p></figcaption></figure>

</div>

{% hint style="info" %}
Mirror currently provides **two** NetworkTransform variations:

* Reliable: low bandwidth, same latency as Rpcs/Cmds/etc.
* Unreliable: high bandwidth, extremely low latency

Use NetworkTransform**Reliable** unless you need super low latency.


{% endhint %}



A game object with a Network Transform component must also have a Network Identity component. When you add a Network Transform component to a game object, Mirror also adds a Network Identity component on that game object if it does not already have one.

By default, Network Transform is server-authoritative unless you change the Sync Direction to Client To Server. Client Authority applies to player objects as well as non-player objects that have been specifically assigned to a client, but only for this component. With this enabled, position changes are send from the client to the server.

You can use the **Sync Interval** to specify how often it syncs (in seconds).

### Smooth Movement during Latency, Packet Loss & Jitter

`NetworkTransform` sends movement updates every `sendInterval` over the `Unreliable` channel.

Network conditions are never ideal, so those updates may come in out of order, delayed or get dropped somewhere on the way.

Smooth movement over non-ideal network conditions is one of the more difficult problems in game networking. Our `NetworkTransform` component solves it by using [Snapshot Interpolation](snapshot-interpolation.md). We recommend reading through the linked chapter to understand it in detail.

In short, smooth movement over non-ideal conditions is achieved through buffering. The worse the conditions, the higher the **Buffer Time Multiplier** needs to be. However, the higher it is the longer it buffers too.&#x20;

The total buffer time is calculated by `sendInterval * Buffer Time Multiplier`. It's usually recommended to use a factor of '3'.&#x20;

This means that while you can **increase** the `Buffer Time Multiplier` to make up for worse conditions, you could also **decrease** the `sendInterval` to still keep a reasonably low buffering delay.

Our new Network Transform is already used in production by several real world game projects. Check out [this video](https://www.youtube.com/watch?v=z2JpT\_qLmzk) for a comparison between our old & new **Network Transform** components.



{% hint style="info" %}
Note:\
In Newer Mirror versions, sync interval has moved to NetworkManager as "Send Rate".\
Send rate of 10, would be 0.1 sync interval.
{% endhint %}
