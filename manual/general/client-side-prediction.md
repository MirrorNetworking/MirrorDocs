---
description: Everything you need to know about Mirror's new Prediction algorithm!
---

# Client Side Prediction

<figure><img src="../../.gitbook/assets/2024-03-21 - 12-13-11@2x.png" alt=""><figcaption><p>Billiards is the perfect example to learn about Prediction.</p></figcaption></figure>

{% hint style="warning" %}
Mirror is currently experimenting with various Prediction algorithms. This is all purely experimental, we don't recommend using this just yet.
{% endhint %}



Let's walk through why we even need prediction, how it works, how we implemented it and what you can use it for in your game **today**.&#x20;

{% hint style="info" %}
Please read until the end to understand the **back story** of our Prediction algorithm!
{% endhint %}

## Why we need Prediction

First things first: let's try Billiards **without prediction** to see why we need it!

* Open the **Examples/Billiards** demo
* Ensure NetworkManager -> LatencySimulation adds some latency (50ms)
* Build it, select **Server Only**
* Play in Editor, select **Client** to connect to your build
* Click and drag the white ball to apply a force

You **should** feel a noticeable **delay** from applying force to seeing the physics respond!

Emphasis here is on _"**should feel"**_: if this is not a problem in your game, then you **don't need prediction!**

For example, there were many popular MMOs in the 2000s where you simply had to wait for inputs and that was okay. The same is true for card games, strategy games and perhaps even most games in general - it's often okay to wait 50ms.

However - for fast paced games like shooters, VR, or even simple physics games like Billiards - having a noticeable delay can be completely unacceptable. As you may have guessed, there is a solution and it's called Prediction!

{% hint style="info" %}
Prediction means simulating actions on the client immediately in order to avoid delay.\
Once the server state comes back, we compare the prediction and correct it if necessary!
{% endhint %}

If that definition confuses you, let's explain it again in more detail.

Usually, this is what happens when the client wants to apply force to a billiards ball:

* Client sends `[Command] CmdApplyForce(force)` to server (... this takes 50 ms)
* Server executes `Rigidbody.AddForce(force)`
* Server syncs new Rigidbody positions to client (... this takes 50 ms)
* Client sees the result, in this case 50ms + 50ms = 100 ms later

Now we might think, okay, let's just apply the force on the client immediately:

* Client executes `Rigidbody.AddForce(force)`
* This happens locally, neither the server nor other clients see it :sob:

Okay so, what if we apply the force locally AND sync it to server:

* Client executes `Rigidbody.AddForce(force)`
* Client sends `[Command] CmdApplyForce(force)` to server (... this takes 50 ms)
* Server executes `Rigidbody.AddForce(force)` as well
* Everyone is happy... right? Wrong! :disappointed\_relieved:

Unfortunately, most Physics engines (including Unity's PhysX) are **not deterministic**. Which means that applying force on client will give slightly different results on the server. And those difference very quickly add up to the point where the balls are completely out of sync about half a second later. Yep, this sucks. The reason for it is that 'floating-point' operations aren't deterministic. If we calculate `Rigidbody.position += Vector2.up` on two different machines, we get ever so slightly different results. This difference adds up to massive desync in just under a second.

Now you may think: why don't we use **deterministic Physics** engines then? Well for one, because Unity just doesn't have one. And secondly, it's a lot of work to build one. And third, it's slower than regular physics since instead of floating-point numbers, we need fixed-point numbers which effectively need twice as many operations.

So, the next question is, what's the solution then? If client should simulate immediately but client and server always drift apart, then what can we do?

Well, the easiest solution would be this:

* Client executes `Rigidbody.AddForce(force)`
* Client sends `[Command] CmdApplyForce(force)` to server (... this takes 50 ms)
* Server executes `Rigidbody.AddForce(force)` as well
* Server syncs new Rigidbody positions to client (... this takes 50 ms)
* Client compares positions and hard corrects its own simulation if necessary.

Notice how there's two "... it takes 50 ms" in there! The client has already moved on and now it suddenly gets server state that's 50 + 50 = 100 ms old. Correcting to this state would have to major issues:

1. The state is from 100 ms ago. The client's current state would pretty much always be elsewhere already, causing corrections all the time.
2. The corrections would set it to where it was 100 ms ago, meaning the balls would always jump noticeably backwards every time.

So, what's the solution then?

Well, it's actually quite simple in theory. The client just needs to store a history of positions:

* Client executes `Rigidbody.AddForce(force)`&#x20;
* _(Client saves Rigidbody.position every 50 ms for comparisons later)_
* Client sends `[Command] CmdApplyForce(force)` to server (... this takes 50 ms)
* Server executes `Rigidbody.AddForce(force)` as well
* Server syncs new Rigidbody positions to client (... this takes 50 ms)
* Client compares with the Rigidbody's positions 50+50=100 ms ago!
  * If there's a mismatch, then do the corrections.

As result, it doesn't matter if we got the server state 50ms, 100ms or 150 ms later.\
The client just checks in history \[- 100 ms] and compares!

Visualized, it may look like this, where the white boxes are the history of positions:

<figure><img src="../../.gitbook/assets/2024-03-21 - 13-15-30@2x.png" alt=""><figcaption></figcaption></figure>

Okay now, we are almost there.

**The last question is: how do we apply corrections?**

Currently we change the position where it was 100 ms ago. But how do we make it have an effect on where the billiards ball is **right now**?

Well, how about this: we apply the deltas. If the ball was at (1,2,0) before, and has since moved a bit forward and a bit to the right - then correct it to something like (1.1, 2, 0) and then move it 'a bit forward' and 'a bit to the right' on top of it again.

To summarize:

{% hint style="success" %}
Prediction works by **keeping** a history, **correcting** the past and **rewinding** the deltas on top.
{% endhint %}

If none of this makes any sense, no worries.

You don't actually have to do any of this yourself. As usual, Mirror takes care of everything for you!

Anyway, let's try it in action then!

## Predicted Billiards

Now that we understand what Prediction is and how it works, let's actually try it right now!

* Open the Examples/**BilliardsPredicted** example from latest Mirror on **GitHub**.
  * The Asset Store Mirror version may have this demo, but it's not up to date yet as of March 2024. Please grab it from [GitHub Releases](https://github.com/MirrorNetworking/Mirror/releases).
* Build it, select **Server Only** in the build, and **Login** the client in your Unity Editor.
* Click and drag the white ball to apply a shot to it.
* Notice how physics react **instantly** without any latency.
* Try increasing the latency in our [Latency Simulation](../transports/latency-simulaton-transport.md) component to 50ms.
* Build and try again - even with latency, reactions are instant!

<figure><img src="../../.gitbook/assets/2024-03-21 - Predicted Billiards Release.png" alt=""><figcaption></figcaption></figure>

Did you notice the transparent ghost objects? When using PredictedRigidbody, there are always:

* **The (transparent) predicted physics object:** this is the Rigidbody which predicts ahead and has corrections applied to it.
* The **(rendered)** original object: this is 'your' original object without physics. This is what the player sees. It automatically follows the physics object but with some smoothing applied.
* The **(transparent)** remote state object: this is for debugging. It shows you the latest server state of the object.

## Adding PredictedRigidbody to your game

If you want to add prediction to the networked Rigidbodies in your game, it's super easy!



**First**, Add the **PredictedRigidbody** component to your prefab:

<figure><img src="../../.gitbook/assets/2024-03-21 - 13-48-29@2x.png" alt=""><figcaption></figcaption></figure>

**Second**, simulate physics on client **AND** send the command to the server:

```csharp
// handle inputs on client
void HandleClick()
{
    GetComponent<Rigidbody>().AddForce(force); // simulate on client
    CmdAddForce(force); // and let the server know
}
```

**Third**, simulate physics on server **INSIDE** the command:

```csharp
[Command]
void CmdAddForce(Vector3 force) 
{
    GetComponent<Rigidbody>().AddForce(force); // simulate on server
}
```

And that's all! `PredictedRigidbody` takes care of all the magic for you!

{% hint style="danger" %}
But wait, there's _one more thing... we need to understand **Prediction Mode**!_
{% endhint %}

## Smoothing Mode

<figure><img src="../../.gitbook/assets/2024-03-28 - 22-14-15@2x.png" alt=""><figcaption></figcaption></figure>

Prediction & corrections are always hard applied to the Rigidbody. In order to smooth out results & jitter, there are two different ways for motion smoothing:

* **Smooth**: once the Rigidbody starts moving, all the physics components (Rigidbody+Colliders) are automatically moved onto an invisible Ghost object. The Renderer stays on the original object and smoothly interpolates behind the Ghost object. This gives very smooth results, but there's the extra cost of creating & destroying ghosts and smooth following them.
* **Fast**: here everything remains on the original GameObject. Renderers are always where physics are. This looks snappier, a bit harsher, less smooth. However, it's also significantly faster.

{% hint style="success" %}
We are still evaluating both modes for different demos & game projects. The goal is to settle with one mode eventually. The best way to get a feeling for both modes is to try them in our **BilliardsPredicted** demo yourself. Just change them on both prefabs and see the differences.
{% endhint %}

If you choose **Smooth** mode, then the rest of your game needs to prepare for the fact that the object's Rigidbody+Colliders are moved out of and back into the object all the time. Do what we explain below to make this work seemlessly - in fact, if you do it all the time then you can easily switch modes without breaking things. If you don't want to do any of these changes, then just stick with **Fast** mode.

Alright so the solution is to just grab the Rigidbody from our PredictedRigidbody component all the time:

```csharp
// PROPERLY handle inputs on client
void HandleClick()
{
    // Rigidbody access via PredictedRigidbody 
    Rigidbody rb = GetComponent<PredictedRigidbody>().predictedRigidbody;
    rb.AddForce(force); // simulate on client
    CmdAddForce(force); // and let the server know
}
```

```csharp
[Command]
void CmdAddForce(Vector3 force) 
{
    // on server, this is still fine since Rigidbody always remains on the object
    GetComponent<Rigidbody>().AddForce(force); // simulate on server
}
```

Yep, this is actually it. Everything you need are 4 lines of code and 1 component!

Just to repeat this one more time:

It's important to understand that once you add PredictedRigidbody with **Smooth Mode** to an object, it will automatically separate the Rigidbody & Colliders into a Ghost object while predicting!

{% hint style="danger" %}
Please keep this in mind.&#x20;



`GetComponent<Rigidbody>()` won't always be available while predicting.

`GetComponent<Collider>()` won't always be available while predicting.

`OnCollisionEnter/Exit()` won't always be called while predicting.

`OnTriggerEnter/Exit()` won't always be called while predicting.
{% endhint %}

You probably won't need this yet, but for the future if you want to get OnCollision callbacks to work:

```csharp
// for OnCollision, move the code from your predicted object
// to the 'other' object instead and grab the original from the collider:
void OnCollisionEnter(Collider collider)
{
    // check if the collider is from a predicted object
    // (which means it may be on the original, or on the ghost object)
    if (PredictedRigidbody.IsPredicted(collider, out PredictedRigidbody original)
    {
        Debug.Log("Collided with {collider} which belongs to {original}");
    }
}
```

Just check the **PredictedBilliards** example's code - it's not actually too difficult!

## Predicting other Types

This article focuses on `PredictedRigidbody` - which is a complete component that just works - but only for Rigidbodies.

If you want to predict other types like CharacterControllers, there's good news and bad news:

* The **Good News** is that the underlying Prediction & Correction algorithms are kept generic. You can find them in Prediction.cs and use them for other types easily.
* The **Bad News** is that they are just standalone algorithms. If you want to make an easy-to-use component like PredictedCharacterController, then it's still a bit of work on top of it.&#x20;

We recommend you check out the code in PredictedRigidbody.cs to get a feeling for the separation between high level component and low-level algorithm, and to see how much extra work is needed to predict a specific type like Rigidbody.

To summarize: you _can_ use Mirror's prediction for other types, but you _will_ have to do some work.

For what it's worth, the hard part (the algorithm) works and comes with heavy test coverage!

## Mirror's Prediction for Large Physics Scenes

Now there's one last detail that you absolutely must understand about Mirror's prediction.

Traditionally, Prediction algorithms always roll back and resimulate **the whole physics scene.**\
Because if we want to resimulate a `Rigidbody`, obviously we need `Physics.Simulate()`:

```csharp
// grab simulation 100 ms ago
WorldSnapshot state = history[-100ms];

// compare with server state
if (state != serverState)
{
    // resimulate everything
    state = serverState;
    Physics.Simulate();
    
    // ... do this again for 50ms ago, 25ms ago, 0ms ago etc.
}
```

While this is the most **correct** solution for prediction, there is a downside: performance.

Re-simulating large physics scenes multiple times per correction is super expensive!

While this gives the best results, it's very CPU heavy and doesn't scale well to large scenes.

This is **not** what Mirror does!

{% hint style="success" %}
Mirror's prediction runs without **`Physics.Simulate()`**.
{% endhint %}

Yes, you read that right! But nothing is for free, so let's keep reading first...

Mirror's Prediction was developed in collaboration with a game studio aiming to build networked physics scenes with thousands of predicted Rigidbodies. Using **Physics.Simulate()** was never an object for our implementation since it would not scale, so we had to be creative.

At first we thought: there is no way to scale prediction to that many objects. But there was a catch: while the scene has thousands of predicted Rigidbodies, only a few of them are interacted with at any give time by the local player. For example, in most games you may grab a bottle or kick ladder down, but you rarely interact with thousands of Rigidbodies at the same time.&#x20;

While there are games where interacting with thousands of Rigidbodies is necessary (i.e. destruction type games), this was not the case here. So we thought: why don't we try to manually resimulate individual Rigidbodies without using PhysX!

While we didn't believe it was going to work, we didn't have a choice. So, we set out to try it anyway.&#x20;

## History

We started with a very simplified example that's mostly in 2D: predicted Billiards - the example that you can find in your Mirror folder today.

<figure><img src="../../.gitbook/assets/2024-03-21 - 14-46-53@2x.png" alt=""><figcaption></figcaption></figure>

Specifically, we tried to manually resimulate Rigidbody.position/rotation/velocity/angularVelocity in our C# code, outside of any physics engine. This kinda worked for the first few months, but it never really looked good enough for a production game. But again - we didn't have a choice so we just kept at it. After 4 months of debugging, we managed to fix a few miscalculations, inconsistencies and mis-predictions.&#x20;

<figure><img src="../../.gitbook/assets/2024-03-21 - 14-49-29@2x.png" alt=""><figcaption><p>We debugged mispredictions with lots of Gizmos, Debug.DrawLine and stepping through recordings frame by frame.</p></figcaption></figure>

Our Predicted Billiards demo actually ended up working quite well - much better than we anticipated. So it was time to port this to a real game!

... Almost as expected, everything broke and the predictions looked terrible in a more complex scene. But once again, we didn't have a choice so we just kept debugging and painfully fixing one issue after another. In particular, we had to add support for all types of Colliders and Joints, as well as Rigidbodies on child objects. Three months later - and to everyone's surprise - this actually worked really well!

Unfortunately we can't show any videos of the game, but just to summarize this once more:

{% hint style="success" %}
Mirror's prediction works **really well** for **large physics scenes** where the player only **interacts** **with a few objects** at a time.

Our algorithm **sacrifices accuracy for performance!**
{% endhint %}

In other words: it works great for the games that we developed it for.

It may or may not work for your game, because we still need to test it with more complex physics!

## Worst Case Benchmark

Mirror's prediction is optimized for large physics scenes where the player only interacts with a few objects at a time. However, we still built a worst-case benchmark where you can spawn a few hundred (or thousands of) objects that are predicted all the time.&#x20;

We are using this benchmark for profiling and performance optimizations. Feel free to check it out in **Examples/BenchmarkPrediction**, it's probably the easiest prediction example that you'll find!

<figure><img src="../../.gitbook/assets/2024-03-21 - 19-03-23@2x.png" alt=""><figcaption></figcaption></figure>

## What's Next

After supporting interactable objects, our two major upcoming goals are:

* Complex physics with stacked objects
* Predicted player movement

We are working on predicted stacked objects right now. As of March 2024, they generally sync well, but don't properly come to rest just yet. Just like with the early billiards demos, they don't look good enough for production games just yet! Once they work well, we will release a demo for this too.



{% embed url="https://www.youtube.com/watch?v=WI0oV8ZSbUo" %}

Predicted player movement has not yet been tested whatsoever. It may or may not work, and we will most likely need to add a 10% tolerance to say: accept 10% of mispredictions instead of hard correcting them always. This is because for players, smoothness while letting them mispredict a little bit should be a worth tradeoff over correcting all the time.

Once this works well, we will build a demo!

{% hint style="info" %}
Not using Physics.Simulate() is a risky approach. It's certainly possible that we will hit a local maximum and can't improve it any further without using Physics.Simulate(). For now, that's not an option.
{% endhint %}

{% hint style="info" %}
Prediction will remain our focus for the rest of the year 2024.

Let's see where it goes, please try it and report back! :rocket:
{% endhint %}
