---
description: Mirror's Inofficial Edgegap Hosting Plugin documentation.
---

# Edgegap Hosting Plugin Guide

[https://app.edgegap.com/user-settings?tab=tokens](https://app.edgegap.com/user-settings?tab=tokens)Thanks to Edgegap's hosting plugin for Unity, Mirror users get 0.5 vCPU cloud hosting for free!

You can build & launch a game server into the cloud directly from your Unity Editor, without any Linux or Cloud usage whatsoever.

{% hint style="warning" %}
Edgegap provides an official Asset Store plugin and Documentation. Avoid those for now.
{% endhint %}

<figure><img src="../.gitbook/assets/2023-11-03 - 17-31-21@2x.png" alt=""><figcaption><p>Edgegap Hosting Plugin</p></figcaption></figure>

{% hint style="danger" %}
This is **cutting edge**, **next gen**, **black magic** technology with a **lot** of rough edges!

If you encounter issues (you will), report them in our Discord's **#edgegap** channel.

\=> Today, the plugin requires \~30 clicks and has lots of issues.

\=> Long term, more and more steps will be automated until it's only 1 click!

\
Enjoy a preview of the future, and please be patient with our 🇨🇦 Canadian friends!\
We promise to make this super easy for you as we go.
{% endhint %}

## Open the Plugin in Unity / Mirror

As of November 2023, Mirror now has the plugin included by default.\
Originally it comes from the Unity Asset Store, but we already fixed some of the issues.

{% embed url="https://assetstore.unity.com/packages/tools/network/edgegap-cloud-server-212563" %}

We do not recommend to use the Asset Store version. Instead, download latest Mirror from Github, or simply download the Assets/Mirror/Hosting folder from Github and drop it into your Mirror project.

Once you have, check for "Edgegap -> Edgegap Hosting" in Unity's top menu:

<figure><img src="../.gitbook/assets/2023-11-03 - 17-49-38@2x.png" alt=""><figcaption></figcaption></figure>

## Create an Edgegap Account

As mentioned, Edgegap gives you 0.5 vCPU for free. In other words, 50% of a virtual CPU core in the cloud. That's enough to test simple games. We hope that this increases to 1 vCPU long term.

Of course, you can always have more vCPUs if you send them money.

For now, let's start by creating an account on the Edgegap website. It's free.

{% embed url="https://app.edgegap.com/auth/register" %}

## Create an Application

Next, go to **Applications**:

<figure><img src="../.gitbook/assets/2023-11-03 - 17-54-09@2x.png" alt=""><figcaption></figcaption></figure>

Then click **Create application** in the top right and enter a **lowercase(!)** name like:

<figure><img src="../.gitbook/assets/2023-11-03 - 18-00-37@2x.png" alt=""><figcaption></figcaption></figure>

The image doesn't matter. Just click **Create Application**.

Make sure to replace 'your-game' with your game name, and do that in all the following steps where it says 'your-game' as well. Continuing, fill out the application details exactly like this:

<figure><img src="../.gitbook/assets/2023-11-03 - 18-02-46@2x.png" alt=""><figcaption></figcaption></figure>

* **Version name**: just use exactly **v1**, don't worry about this. (**lowercase!)**
* **Registry**: use exactly **harbor.edgegap.net** (**lowercase!)**. You can enter a custom docker registry here, but you probably don't have one or don't know what this even means. That's fine.
* **Image repository**: pick something. For example: valve/counter-strike if you are Valve and your game is Counter-strike. (**lowercase!)**. If you don't have a company, make something up.
* **Tag**: 0.0.1 - use exactly this. Every new server build will automatically increase this to 0.0.2 etc.
* **vCPU**: 0.5vCPU is free.
* **Memory**: 0.5 GB is free.
* **Private Registry User**: we need to enter our credentials for **harbor.edgegap.net** here. We'll get them later.&#x20;

{% hint style="warning" %}
As of November 3, 2023, modifying an Application's **Private Registry User** is broken. You may need to create a new application once you have the credentials later. In order to keep the natural flow of the tutorial, we still create the Application first and hope that Edgegap will fix this soon.
{% endhint %}

Next, click **Create Version**. Note it says 'Version'. You have different Versions of your game, kind of like 'Counter-Strike 1.6, Counter-Strike GO, Counter-Strike 2' etc. For now, don't worry about versions. One is enough.

After clicking **Create Version**, you will see a validation error.

<figure><img src="../.gitbook/assets/2023-11-03 - 18-10-18@2x.png" alt=""><figcaption></figcaption></figure>

This is shown because we didn't upload a docker image to the registry yet. Don't worry about it. Just click **Continue Anyway.**

Next, it asks us to create a port. Use the one from your Unity Project's NetworkManager -> Transport. For example, let's do this with Mirror's **Tanks** demo:

<figure><img src="../.gitbook/assets/2023-11-03 - 18-13-05@2x.png" alt=""><figcaption></figcaption></figure>

Kcp is **UDP** with port **7777** by default.\
If you use Telepathy, then that would be TCP with port 7777 by default.

Let's enter **7777** and **UDP** in our application.

We also disabled **Verifications** in order to keep things simple.

<figure><img src="../.gitbook/assets/2023-11-03 - 18-15-07@2x.png" alt=""><figcaption></figcaption></figure>

Finally, press **Submit**. There you go, your first application.&#x20;

It doesn't do anything yet, but we'll get there.

To compare, this is what it looks like now. Note there's a red **!** sign without telling you why. That's fine, I guess:

<figure><img src="../.gitbook/assets/2023-11-03 - 18-20-12@2x.png" alt=""><figcaption></figcaption></figure>

## Get Registry Access

Next, we need access to Edgegap's Docker registry in order to push our server builds there.

If you aren't familiar with Docker, don't worry about it.

For now, let's just get access to the registry.

Click Container Registry in the left menu, or follow this link:

{% embed url="https://app.edgegap.com/registry-management/repositories/list" %}

You'll probably see a screen where you can request access.&#x20;

Do request, and then it'll look like this:

<figure><img src="../.gitbook/assets/2023-11-03 - 18-23-42@2x.png" alt=""><figcaption></figcaption></figure>

Now we have to wait for an Edgegap employee to accept. We recommend to ask someone from the Edgegap team in the Mirror discord's #edgegap channel directly to speed this up.

{% hint style="warning" %}
Obviously this step does not spark joy.\
We are working with Edgegap to make this easier :/

For now, just hang in there.
{% endhint %}

Once you have registry access, grab your **Username** and **Token** from the **Container Registry** page:&#x20;

<figure><img src="../.gitbook/assets/2023-11-03 - 18-29-23@2x.png" alt=""><figcaption></figcaption></figure>

Go back to Applications, select your previously created Application, then select v1:

<figure><img src="../.gitbook/assets/2023-11-03 - 18-31-39@2x.png" alt=""><figcaption></figcaption></figure>

Then click **Edit** to enter your **Private Registry** username and token from the **Container Registry** page above:

<figure><img src="../.gitbook/assets/2023-11-03 - 18-32-01@2x.png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
If saving doesn't work, delete the Application and create it again with the Private Registry credentials directly. Edgegap is aware of the bug, feel free to complain in their Discord if it bothers you :)
{% endhint %}



## Create a Token

Last step on the website, we need a **Token** for the Unity plugin later. Since we are here, let's just create it now.

On the Edgegap website, click your Organization on the bottom left (the head icon), click Tokens or follow this link [https://app.edgegap.com/user-settings?tab=tokens](https://app.edgegap.com/user-settings?tab=tokens) and then click **Create API Token**:

<figure><img src="../.gitbook/assets/2023-11-03 - 18-38-00@2x.png" alt=""><figcaption></figcaption></figure>

Name it **your-game-token**, enable **is Quick Start** and press **Submit**:

<figure><img src="../.gitbook/assets/2023-11-03 - 18-41-19@2x.png" alt=""><figcaption></figcaption></figure>

Afterwards click the Clipboard icon to copy it to your clipboard:

<figure><img src="../.gitbook/assets/2023-11-03 - 18-41-52@2x.png" alt=""><figcaption></figcaption></figure>

## Configuring the Edgegap Unity Plugin

Alright, back to Unity. Let's punch in all the data in our Plugin. As mentioned, you can find it under Edgegap -> Edgegap Hosting in Unity's top menu. Let's configure it:

<figure><img src="../.gitbook/assets/2023-11-03 - 18-36-14@2x.png" alt=""><figcaption></figcaption></figure>

* **Token**: that's the one we just copied. Put it in there. It looks like "token abcd-efgh-ijkl....". Make sure to include the "token " part. If you just paste it, then it's fine as is.
* **API Environment**: use **Console**. I don't know why. Staging doesn't work.
* **App Name:** your-game, or whatever you entered in your Application before.
* **App version**: v1 as we entered in our Application.



