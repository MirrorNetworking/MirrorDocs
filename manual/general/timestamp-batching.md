# Timestamp Batching

Let's learn how Mirror sends around messages.

## Batching

Every message that you send will be batched until the end of the frame in order to minimize bandwidth and transport calls. For example, if you send a lot of 10 byte messages then we can usually fit \~120 of them into one **MTU** sized batch of around 1200 bytes.

For the Transport, it's pretty convenient to send around messages in 1200 byte chunks _(see_ [_MTU_](https://en.wikipedia.org/wiki/Maximum\_transmission\_unit)_)_. Messages larger than **MTU** are sent as a single batch. To be exact, the Transport decides the batch siz that Mirror aims for via `Transport.GetBatchThreshold()`.

Mirror batching is bidirectional. Which means that both the client and the server batch their messages and flush them out at the end of the frame.

{% hint style="success" %}
In short, batching significantly reduces bandwidth and improves performance.
{% endhint %}

## Timestamps

For some networking components, it's useful to know exactly when a message was _sent by the remote_.

For example, `NetworkTransform` receives the server's positions and then interpolates between them. For a smooth interpolation, we need to exactly reconstruct what happened on the server. For that, we need to know _when_ an object has been at a certain position on the server.

The obvious solution is to simply send both `timestamp` and `position` every time:

```csharp
[Rpc]
public void RpcPositionUpdate(float timestamp, Vector3 position)
{
    // ...
}
```

In fact, that's what an early version of our new `NetworkTransform` component did.

For the above code, we pay a significant bandwidth cost because for every position message, we also need to include a 4 byte `float` (or even better, an 8 byte `double` for higher precision). When synchronizing large worlds, the bandwidth would add up quickly.

`NetworkTransform` is only one of many components. Several others might need timestamps too, which would increase bandwidth even further.

To make life easier, Mirror includes _an 8 byte_ `double` _precision_ `timestamp` in every Batch. Instead of including it in every message, we include it once per \~1200 byte batch which is barely noticeable when it comes to bandwidth.

For any message handler in Mirror, you can get the timestamp from the batch it arrived with via `NetworkConnection.remoteTimeStamp`.

* On the **client**, all object data arrives in messages/batches from the server. So at any given time, you can find out when an object's `Rpc`/`OnDeserialize`/`OnMessage` handler was sent by the server via  `NetworkClient.connection.remoteTimeStamp`.
  * Note that on the client, we don't use an object's `connectionToServer` because only the player owned objects have connections to the server. Instead we use the client's `NetworkClient.connection` to server, which is always guaranteed to be there.
* On the **server**, only player owned objects get messages from player connections. So at any given time, you can find otu when object's `Cmd`/`OnDeserialize`/`OnMessage` handler was sent by the client via `connectionToClient.remoteTimeStamp`.

{% hint style="info" %}
**Timestamp Batching** is Mirror's unique approach to general purpose world synchronization. \
\
For example, Quake's Delta Snapshots are ideal for FPS games where the whole world fits into one world state message, while not being ideal for larger MMO sized worlds with lots of entities. Or perhaps you are working on a multiplayer text adventure with barely any world state, but still lots of network messages.\
\
**Timestamp Batching** fits well into Mirror's architecture. It should help you to reduce bandwidth no matter what type of project you work on.
{% endhint %}
