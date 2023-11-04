---
description: Mirror's inofficial Edgegap Hosting Plugin documentation.
---

# Edgegap Hosting Plugin Guide

Thanks to Edgegap's hosting plugin for Unity, Mirror users get 0.5 vCPU cloud hosting for free!

You can build & launch a game server into the cloud directly from your Unity Editor, without any Linux or Cloud usage whatsoever.

{% hint style="warning" %}
Edgegap provides an official Asset Store plugin and Documentation. Avoid those for now.
{% endhint %}

<figure><img src="../.gitbook/assets/2023-11-03 - 17-31-21@2x.png" alt=""><figcaption><p>Edgegap Hosting Plugin</p></figcaption></figure>

{% hint style="danger" %}
This is **cutting edge**, **next gen**, **black magic** technology with a **lot** of rough edges!

If you encounter issues (you will), report them in our Discord's **#edgegap** channel.

\=> Today, the plugin requires \~30 clicks and has a few issues.

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
Obviously waiting for manual acceptance does not spark joy.\
We are working with Edgegap to make this easier.

For now, just hang in there.
{% endhint %}

Once you have registry access, grab your **Username** and **Token** from the **Container Registry** page:&#x20;

<figure><img src="../.gitbook/assets/2023-11-03 - 18-29-23@2x.png" alt=""><figcaption></figcaption></figure>

Go back to Applications, select your previously created Application, then select v1:

<figure><img src="../.gitbook/assets/2023-11-03 - 18-31-39@2x.png" alt=""><figcaption></figcaption></figure>

Then click **Edit** to enter your **Private Registry** username and token from the **Container Registry** page above:

<figure><img src="../.gitbook/assets/2023-11-03 - 18-32-01@2x.png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
If saving doesn't work, delete the Application and create it again with the Private Registry credentials directly. Edgegap is aware of the bug, this should be solved soon!
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
* **API Environment**: use **Console**. We don't know why 'Staging' didn't work.
* **App Name:** your-game, or whatever you entered in your Application before.
* **App version**: v1 as we entered in our Application.

Next press **Connect**. This generally works if you have the correct token. You should see 'Connected' now:

Next, enter the other details from our Application:

<figure><img src="../.gitbook/assets/2023-11-03 - 18-50-31@2x.png" alt=""><figcaption></figcaption></figure>

* **Container Registry**: harbor.edgegap.net
* **Image Repository**: your-company/your-game or whatever you entered in your Application before
* **Tag**: 0.0.1 for now.&#x20;
* **Increment tag on build**: enable this. You always want to increase the tag for each new build to avoid caching issues. Seriously, don't push a new server build with an old tag, it's probably gonna launch the old build again due to caching.

## Build and Push

Next, we are going to build our project as Linux game server, and create a Docker build, then push it to Edgegap. This is actually pretty easy, just need to install a few things really quick.

### Install Unity Linux Build Support

In your **Unity Hub**, select **Installs**, press the Configuration icon next to your Unity version and click **Add Modules**:

<figure><img src="../.gitbook/assets/2023-11-03 - 18-52-53@2x.png" alt=""><figcaption></figcaption></figure>

Find and install **ALL** Linux Support options. I don't know which one Edgegap requires, so we install all of them:

<figure><img src="../.gitbook/assets/2023-11-03 - 18-54-28@2x.png" alt=""><figcaption></figcaption></figure>

Press Continue, wait, restart Unity once, done.

### Install Docker Desktop

Edgegap works with containers, which means we need to install Docker. We don't need to worry about it ever, we just need to install it once.

The easiest way is to simply install **Docker Desktop**:

{% embed url="https://www.docker.com/products/docker-desktop/" %}

Download it, install it, open it and leave it running. You can pretty much auto start it with your operating system each time.

{% hint style="info" %}
Quick explanation about Docker if you care. You don't need to know this, so feel free to skip.

Basically Docker is a super easy way to configure a virtual machine for your game server build. Previously you would manually create a VM in say Google Cloud, configure a hard disk, open ports, install a Linux version, run apt-update, install dependencies, ...

With docker, we just have a text file. It says 'install ubuntu, copy our build into the VM, navigate to the folder, run unity. Again, you DO NOT need to worry about this. The plugin creates this automatically, something like this (again, don't worry about it):

```
FROM ubuntu:bionic
ARG DEBIAN_FRONTEND=noninteractive
COPY Builds/EdgegapServer /root/build/
WORKDIR /root/
RUN chmod +x /root/build/ServerBuild
ENTRYPOINT [ "/root/build/ServerBuild", "-batchmode", "-nographics"]

```
{% endhint %}

Next, we need to log into Edgegap's docker registry that we previously requested access to.

We are working with Edgegap to automate this. For now you need to open a Terminal / Console:

* On Windows, hit CTRL+R, enter CMD, hit enter to open it
* On Mac, open Finder, go to Applications -> Utilities -> Terminal
* On Linux, you'll figure it out

{% hint style="info" %}
Don't be scared of the Terminal. It's just black background and white text where we'll enter exactly one command. This will be automated soon.
{% endhint %}

Now enter this simple terminal command. There won't be any others, promised!

```
docker login harbor.edgegap.net
```

<figure><img src="../.gitbook/assets/2023-11-03 - 19-07-02@2x.png" alt=""><figcaption></figcaption></figure>

It'll ask you for the Username and Token that we see on Edgegap.com -> Container Registry. Punch it in there, done. Docker desktop seems to remember this, so you won't need to do this again after restarting next time.

Alright, that's it for Docker.

## Back to the Unity Plugin

Next, go back to the Unity plugin and press **Build and Push**:&#x20;

<figure><img src="../.gitbook/assets/2023-11-03 - 19-09-37@2x.png" alt=""><figcaption></figcaption></figure>

You'll see a progress bar for a while. Unity will create a Linux build, then create a Docker build, then upload the whole thing to Edgegap. Note that uploading will take a while depending on your internet connection. The progress bar halts while uploading, but you can check your operating system's bandwidth usage to see if it works.

<figure><img src="../.gitbook/assets/2023-11-03 - 19-17-10@2x.png" alt=""><figcaption></figcaption></figure>

If this all worked, then it'll simply finish without telling you.

If this failed, it'll show you errors.

Here are a few common issues and workarounds:

* **Missing Linux Build Support**: install it in your Unity hub. Make sure you do it for the Unity version that you are using in your project. This generally works once you have the Linux Build Support installed.
* **Incremental Build Failed**: delete your previous Unity Linux build, restart Unity, just try again. Delete your Library/ folder if you need to. This is a Unity bug that happens sometimes.
* **Docker authorization Failed**: make sure Docker Desktop is running and make sure that you are logged in with the above Terminal command. Then this generally works.

If you encounter other issues, talk in our #edgegap Discord channel. We want to find solutions for any possible issue and explain this here!

{% hint style="info" %}
If building failed, then the progress bar may get stuck forever. You'll have to force kill Unity with your task manager. This will be fixed very soon.
{% endhint %}

## Start Server

Once we finished building, simply press the **Start Server** button. It'll be **Deploying** for a while:

<figure><img src="../.gitbook/assets/2023-11-03 - 19-21-14@2x.png" alt=""><figcaption></figcaption></figure>

Eventually it'll be started:

<figure><img src="../.gitbook/assets/2023-11-03 - 19-21-59@2x.png" alt=""><figcaption></figcaption></figure>

This screenshot is from my other test application which uses TCP port 5000.

For you it should show you UDP port 7777, just as we configured in the Application.

Edgegap will assign you a random **External Port** that maps to your configured Port.

That is because they may have multiple deployments asking for the same port 7777.

But there's only one port 7777.

That's why they have External ports that are random.

Long story short, you may now connect your game!

## Connect your Game Client

Press **Play** in Unity, enter the **Server DNS** from the hosting plugin (there's a **Copy**) button to copy it to your Clipboard. Don't connect yet!

<figure><img src="../.gitbook/assets/2023-11-03 - 19-24-48@2x.png" alt=""><figcaption></figcaption></figure>

Find your **NetworkManager**, find the Transport component, enter the **External Port** from above. In this case, 32392:

<figure><img src="../.gitbook/assets/2023-11-03 - 19-26-31@2x.png" alt=""><figcaption></figcaption></figure>

We are working towards making this easier. For now, just assign it there at runtime. Don't save it.

Next, press **Client** to connect to the server on that port.

If it works, great!

If it doesn't:

* On the Edgegap website, go to Deployments -> select your Deployment -> select **Container Logs**, check the log files to see if your game server actually launched or if there are issues.
  * If it says "exec user process caused: no such file or directory": this can happen if you are trying to build from Apple Silicon (or any other ARM machine), because Edgegap's infrastructure runs on x86. Docker supposedly supports cross compilation now, so we are working with Edgegap to fix this asap.
* If everything seems fine but you still can't connect, please talk to an Edgegap employee  in the Mirror Discord's **#edgegap** channel.&#x20;

{% hint style="warning" %}
As mentioned, this is cutting edge technology, with lots of rough edges. You'll most certainly encounter errors and headaches for now. Please report any issues so Edgegap can fix them.
{% endhint %}

{% hint style="info" %}
We are aware that this guide was quite long.\
There are a lot of steps which we think can be automated.\
We are working on making this easier as we go.\
Long term this should be a super easy solution for hosting, so you'll never have to worry about hosting dedicated servers or in dealing with cloud providers.
{% endhint %}

{% hint style="info" %}
Big thanks to our Candian 🇨🇦 friends at Edgegap for giving Mirror users 0.5 vCPU for free!\
While we hope for a larger free tier in the future, please keep in mind that they are the ones who are paying for the infrastructure. If you have the means, please pay them for more vCPUs!
{% endhint %}
