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

First things first: let's try Billiards **without prediction** to see why we need it!

* Open the **Examples/Billiards** demo
* Ensure NetworkManager -> LatencySimulation adds some latency (50ms)
* Build it, select **Server Only**
* Play in Editor, select **Client** to connect to your build
* Click and drag the white ball to apply a force

You **should** feel a noticeable **delay** from applying force to seeing the physics respond!

Emphasis here is on _"**should feel"**_: if this is not a problem in your game, then you **don't need prediction!**

For example, there were many popular MMOs in the 2000s where you simply had to wait for inputs and that was okay. The same is true for card games, strategy games and perhaps even most games in general - it's often okay to wait 50ms.

However - for fast paced games like shooters, VR, or even simple physics games like Billiards - having a noticeable delay can completely unacceptable. As you may have guessed - there is a solution and it's called Prediction!

{% hint style="warning" %}
Prediction means simulating actions on the client immediately in order to avoid delay.\
Once the server state comes back, we compare the prediction and correct it if necessary!
{% endhint %}

If that definition confuses you, let's explain it again in more detail.

Usually, this is what happens when the client wants to apply force to a the billiards ball:

* Client sends `[Command] CmdApplyForce(force)` to server (... this takes 50 ms)
* Server executes `Rigidbody.AddForce(force)`
* Server syncs new Rigidbody positions to client (... this takes 50 ms)
* Client sees the result, in this case 50ms + 50ms = 100 ms later

Now we might think: okay, let's just apply the force on the client immediately:

* Client executes `Rigidbody.AddForce(force)`
* This happens locally, neither the server nor other clients see it :sob:

Okay so, what if we apply the force locally AND sync it to server:

* Client executes `Rigidbody.AddForce(force)`
* Client sends `[Command] CmdApplyForce(force)` to server (... this takes 50 ms)
* Server executes `Rigidbody.AddForce(force)` as well
* Everyone is happy... right? Wrong! :disappointed\_relieved:

Unfortunately, most Physics engines (including Unity's PhysX) are **not deterministic**. Which means that applying force on client will give slightly different results on the server. And those difference very quickly add up to the point where the balls are completely out of sync about half a second later. Yep, this sucks. The reason for it is that 'floating point' operations aren't deterministic. If we calculate `Rigidbody.position += Vector2.up` on two different machines, we get every so slightly different results. This difference adds up to massive desync in just under a second.

Now you may think: why don't we use **deterministic Physics** engines then? Well for one, because Unity just doesn't have one. And secondly, it's a lot of work to build one. And third, it's slower than regular physics since instead of floating point numbers, we need fixed point numbers - which effectively need twice as many operations.

So the next question is: what's the solution then? If client should simulate immediately but client and server always drift apart, then what can we do?

Well, the easiest solution would be this:

* Client executes `Rigidbody.AddForce(force)`
* Client sends `[Command] CmdApplyForce(force)` to server (... this takes 50 ms)
* Server executes `Rigidbody.AddForce(force)` as well
* Server syncs new Rigidbody positions to client (... this takes 50 ms)
* Client compares positions and hard corrects its own simulation if necessary.

Notice how there's two "... it takes 50 ms" in there! The client has already moved on and now it suddenly gets server state that's 50 + 50 = 100 ms old. Correcting to this state would have to major issues:

1. The state is from 100 ms ago. The client's current state would pretty much always be elsewhere already, causing corrections all the time.
2. The corrections would set it to where it was 100 ms ago, meaning the balls would always jump noticeably backwards every time.

So what's the solution then... ?

Well, it's actually quite simple in theory. The client just needs to store a history of positions:

* Client executes `Rigidbody.AddForce(force)`&#x20;
* _(Client saves Rigidbody.position every 50 ms for comparisons later)_
* Client sends `[Command] CmdApplyForce(force)` to server (... this takes 50 ms)
* Server executes `Rigidbody.AddForce(force)` as well
* Server syncs new Rigidbody positions to client (... this takes 50 ms)
* Client compares with the Rigidbody's positions 50+50=100 ms ago!
  * If there's a mismatch, then do the corrections.

As result, it doesn't matter if we got the server state 50ms, 100ms or 150 ms later.\
The client just checks in history\[- 100 ms] and compares!

Visualized it may look like this, where the white boxes are the history of positions:

<figure><img src="../../.gitbook/assets/2024-03-21 - 13-15-30@2x.png" alt=""><figcaption></figcaption></figure>

_Okay now, we are almost there._

_**The last question is: how do we apply corrections?**_

Currently we change the position where it was 100 ms ago. But how do we make it have an affect on where the billiards ball is **right now**?

Well, how about this: we apply the deltas. If the ball was at (1,2,0) before, and has since moved a bit forward and a bit to the right - then correct it to something like (1.1, 2, 0) and then move it 'a bit forward' and 'a bit to the right' on top of it again.

To summarize:

{% hint style="warning" %}
Prediction works by **keeping** a history, **correcting** the past and **rewinding** the deltas on top.
{% endhint %}

If none of this makes any sense, no worries.

You don't actually have to do any of this yourself. As usual, Mirror takes care of everything for you!

Anyway, let's try it in action then!

## Predicted Billiards

Now that we understand what Prediction is and how it works, let's actually try it right now!

* Open the Examples/**BilliardsPredicted** example from latest Mirror on **Github**.
  * The Asset Store Mirror version may have this demo, but it's not up to date yet as of March 2024. Please grab it from Github: [https://github.com/MirrorNetworking/Mirror/](https://github.com/MirrorNetworking/Mirror/)
* Build it, select **Server Only** in the build, and **Login** the client in your Unity Editor.
* Click and drag the white ball to apply a shot to it.
* Notice how physics react **instantly** without any latency.
* Try increasing the latency in NetworkManager's LatencySimulation component to 50ms.
* Build and try again - even with latency, reactions are instant!

<figure><img src="../../.gitbook/assets/2024-03-21 - Predicted Billiards Release.png" alt=""><figcaption></figcaption></figure>

Did you notice the transparent ghost objects? When using PredictedRigidbody, there are always:

* **The (transparent) predicted physics object:** this is the Rigidbody which predicts ahead and has corrections applied to it.
* The **(rendered)** original object: this is 'your' original object without physics. This is what the player sees. It automatically follows the physics object but with some smoothing applied.
* The **(transparent)** remote state object: this is for debugging, it shows you the latest server state of the object.

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
But wait, there's _one more thing_...
{% endhint %}

Remember how PredictedRigidbody moves your Rigidbody+Colliders onto a ghost object temporarily?

That means GetComponent\<Rigidbody>() wouldn't actually work all the time :sob:.

But no worries, just grab the Rigidbody from our PredictedRigidbody component instead:

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

It's important to understand that once you add PredictedRigidbody to an object, it will automatically separate the Rigidbody & Colliders into a Ghost object while predicting!

{% hint style="danger" %}
Please keep this in mind.&#x20;



`GetComponent<Rigidbody>()` won't always be available while predicting.

`GetComponent<Collider>()` won't always be available while predicting.

`OnCollisionEnter/Exit()` won't always be called while predicting.

`OnTriggerEnter/Exit()` won't always be called while predicting
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

## Mirror's High Performance Prediction under the Hood

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

Resimulating large physics scenes multiple times per correction is super expensive!

While this gives the best results, it's very CPU heavy and doesn't scale well to large scenes.

This is **not** what Mirror does!

{% hint style="success" %}
Mirror's prediction runs without **`Physics.Simulate()`**.
{% endhint %}

Huh? Yes, you read that right!

During 2023, a major game studio contacted us about implementing prediction for large physics scenes with thousands of predicted Rigidbodies where Physics.Simulate() is just not an option.&#x20;

Their setup is as follows: there are thousands of interactable networked Rigidbodies which need to be predicted, but the player only ever interacts with a handful of them at a time.

If you remember, Prediction traditionally calls Physics.Simulate() to resimulate the whole physics scene - in this case, with thousands of Rigidbodies. This was simply not an option for this game, so we had to improvise.

We thought: what if we were to resimulate **only** the objects which the player is currently interacting with?

This would reduce resimulations from 'thousands of objects' to 'a handful of objects'. Of course, without Physics.Simulate() we would never get a 100% accurate resimulation.&#x20;

But how well would it work? Would it be worth the significant performance saving?

...

And so we set out to build Mirror's Prediction with a different mindset: we trade off 10% accuracy vs. 10x performance by not using Physics.Simulate(). We weren't sure if this was going to work - in fact, we didn't think it would. But we wanted to help the studio ship their game, and so our journey began.

At first, we've focused on a super simple demo: predicted Billiards - the one that you've seen above. The early results were pretty bad, but we kept at it. About 5 months later, after countless of iterations and improvements it eventually got better. Today, predicted Billiards looks really good. If you look closely, you can occasionally spot some unrealistic simulations - but for us the 10x performance gain was worth it.

Next, we set out to integrate **PredictedRigidbody** into their game, which is full on 3D with much more complex physics than just Billiards. Once again, it looked pretty bad at first. After more iterations, we got this to work very decently as well - unfortunately we can't show any video of the game here.

{% hint style="success" %}
Long story short, Mirror's prediction is 10x faster, 10% less accurate, and ideal for scenes where only a few physics objects interact with each other at a time.
{% endhint %}

Please understand that we are taking a huge risk with this implementation. While it works for the mentioned game studio, it may not end up working well for scenes with super complex physics interactions, or for predicted players. If you bet your game on our prediction, please be aware of this.

## What's Next

As mentioned, our two major upcoming goals for prediction are:

* Support more complex physics like stacked objects
* Support predicted player movement

We are working on predicted stacked objects right now. As of March 2024, they generally sync well, but don't properly come to rest just yet.

<figure><img src="../../.gitbook/assets/2024-03-21 - predicting stacked objects.png" alt=""><figcaption><p>Testing stacked predicted Rigidbodies with our custom algorithm</p></figcaption></figure>

As for predicted player movement: this will be super useful to avoid both latency and cheating for movement in many game types. The prediction algorithm will be the same - we simply didn't have time to do any tests on this yet - which is why we don't want to recommend doing it just yet.

We will most likely need to add some kind of prediction tolerance to give players 10% more authority instead of correcting every single miss-prediction.

{% hint style="info" %}
Prediction will remain our focus for the rest of the year 2024.

Let's see where it goes, please try it and report back! :rocket:
{% endhint %}
