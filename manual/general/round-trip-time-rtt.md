---
description: Quick Overview on Mirror's RTT, and how to improve it in your game.
---

# Round Trip Time (RTT)

###

### Understanding RTT

{% hint style="info" %}
**Round Trip Time (rtt)** is the time it takes for a message to go to the other end and back.\
RTT always depends on two factors:

1. **Latency:** Network Message take some time to travel over the internet.
2. **Update Interval:** Messages need to be processed and sent back to the other end. If your server's update rate is slow, RTT will be large.
{% endhint %}

Mirror calculates round trip time on the client, and on the server:

* Clients can find it in `NetworkTime.rtt`
* Servers can find it in each `NetworkServer.connection.rtt` (different for each connection)

If you want to display **rtt** in your game, you can use our `NetworkPingDisplay` component.



### Local Machine RTT

If you run our Tanks demo with a server and a client on the same computer, you'll see an average rtt of around 8 ms:

<figure><img src="../../.gitbook/assets/2023-07-18 - rtt 8ms.png" alt=""><figcaption><p>60 Hz update rate gives 8 ms RTT</p></figcaption></figure>

This may seem strange at first: while 8 ms isn't much, it's still not as low as you would expect on your local computer. Clearly packets won't need 8 ms to travel through your memory.

The reason for this is simple: by default, Mirror's NetworkManager.sendInterval is set to 60 Hz.

This means that network updates happen every 16 milliseconds (1 second / 60).

RTT messages may arrive during an update, or while waiting 16 ms for the next update.

On average, this means that there are 8 milliseconds of wait time for each RTT message.

Which is why the above picture shows an RTT of about 8 ms on your local machine.

### Optimizing RTT

A send interval of 60 Hz is a reasonable tradeoff between bandwidth (the more often you send, the larger your bandwidth costs) and RTT. If you want to trade more bandwidth for lower RTT:

* Increase sendInterval to 120 Hz or more
* Make sure that VSYNC is disabled (if your screen is running at 60 Hz, Vsync will cap your update rate to 60 Hz)
* Make sure your game runs fast enough (if your CPU is too slow to achieve 120 Hz, then your send rate obviously can't be 120 Hz)

With an extreme send rate and frame rate, we can easily get the Tanks demo's RTT down to 1-2ms on our local machine. Of course, this isn't recommended in a production game:

<figure><img src="../../.gitbook/assets/2023-07-18 - rtt 2ms.png" alt=""><figcaption></figcaption></figure>

