---
description: >-
  Everything you need to know about Mirror's production ready Prediction
  algorithm!
---

# Client Side Prediction

<figure><img src="../../.gitbook/assets/2024-03-21 - 12-13-11@2x.png" alt=""><figcaption><p>Billiards is the perfect example to learn about Prediction.</p></figcaption></figure>

_**Good news:**_ after 8 months of full time work in collaboration with a game studio, Mirror now has a production ready **Prediction** algorithm!

Let's walk through why we even need prediction, how it works, how we implemented it and what you can use it for in your game **today**.

## Why we need Prediction

To un

{% hint style="info" %}
Prediction is a technique to 'remove' latency on the client.\
\=> Sending to server, simulating on server, applying on client introduces noticeable latency.\
\=> Prediction simulates immediately on client, and only corrects server state if necessary
{% endhint %}

##

Before we explain everything, go ahead and try it for yourself!

* Open the Examples/BilliardsPredicted scene from latest Mirror on Github.
* Build it, select **Server Only** in the build, and **Login** the client in your Unity Editor.
* Click and drag the white ball to apply a shot to it.
* Notice how physics react **instantly** without any latency.
* Try increasing the latency in NetworkManager's LatencySimulation component to 50ms.
* Build and try again - even with latency, reactions are instant!

<figure><img src="../../.gitbook/assets/2024-03-21 - Predicted Billiards Release.png" alt=""><figcaption></figcaption></figure>

This is why prediction is so powerful: we can have physics simulations without waiting for a client->server->client round trip which would cause delays.

This article will explain everything from why we need&#x20;





{% hint style="warning" %}
Please understand that building production ready Client Side Prediction is **very difficult**.\
Mirror is the first _(and only?)_ free open source Unity netcode which decent prediction.\
\
Our approach is **intentionally different** from the usual solution, and **works specifically** for the scenarios that we've focused on for months.\
\
Over time we will expand this to more use cases and make it even easier to use.
{% endhint %}

## How it works



## Mirror's custom Implementation



## What Mirror's Prediction can do



## What's Next
