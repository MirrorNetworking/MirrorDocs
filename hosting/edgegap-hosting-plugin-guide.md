---
description: Mirror's inofficial Edgegap Hosting Plugin documentation.
---

# Edgegap Hosting Plugin Guide

Thanks to **Edgegap**'s hosting plugin for Unity, Mirror users get 0.5 vCPU cloud hosting for free!

You can build & launch a game server into the cloud directly from your Unity Editor, without any Linux or Cloud usage whatsoever.

<figure><img src="../.gitbook/assets/image (144).png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
This is an **early version.**

If you encounter issues, please report them in our Discord's **#edgegap** channel.
{% endhint %}

## Overview

Setup will take only a few minutes, with 4 basic steps:

1. Creating an **Edgegap.com** Account
2. Installing Unity **Linux** Build Support & **Docker** Desktop
3. Configuring the Unity **Plugin**
4. **Building & Pushing** our server to Edgegap

## Open the Plugin in Unity / Mirror

Edgegap's Hosting plugin (version 2) is now always included in the latest Mirror version.

It requires at least Unity 2023 LTS, it won't work with older versions due to using UIToolkit.

The original source is on Github, but we applied a few fixes to the version in Mirror.

{% embed url="https://github.com/edgegap/edgegap-unity-plugin" %}

If you are on an older Mirror version, you can also download the Assets/Mirror/Hosting folder from our Github repository manually. If you see errors about missing 'Newtonsoft Json', add this package to your Package Manager:

<figure><img src="../.gitbook/assets/2023-11-05 - 13-14-02@2x.png" alt=""><figcaption></figcaption></figure>

You can also open the `Packages/manifest.json` file and add this line manually:

```json
"com.unity.nuget.newtonsoft-json": "3.2.1"
```

By default, this should work out of the box though.

Once you have it, check for "**Edgegap** -> **Edgegap** Hosting" in Unity's top menu:

<figure><img src="../.gitbook/assets/2023-12-07 - 13-18-23@2x.png" alt=""><figcaption><p>Edgegap Hosting Plugin V3</p></figcaption></figure>

## Get a Token

The next step is to **Get a Token** from the Edgegap website.

Simply press the **Get a Token** button and either login to your existing Edgegap.com account, or press the **Get Started** link on the page to create a new account.

<figure><img src="../.gitbook/assets/2023-12-07 - 13-21-42@2x.png" alt=""><figcaption></figcaption></figure>



After confirming your email address, you'll be asked about naming **Your Organization**. You can enter anything, in this case we use "**Your-Organization"**.

<figure><img src="../.gitbook/assets/2023-12-07 - 13-55-40@2x.png" alt=""><figcaption></figcaption></figure>

Afterwards you'll be asked to **Begin the Application Tour**. Skip it for now.

Next you'll automatically see the **Token Created Successfully** screen, because we initially clicked the **Get Token** button in the Unity plugin:

<figure><img src="../.gitbook/assets/2023-12-07 - 13-58-35@2x.png" alt=""><figcaption></figcaption></figure>

Press the **Clipboard** icon to copy the token, then paste it back into the Unity plugin and press **Verify.**\
_Note that the token looks like "token 123456-abcdef-00000-..."._\
_The "token ..." part needs to remain in there._

<figure><img src="../.gitbook/assets/2023-12-07 - 13-59-44@2x.png" alt=""><figcaption></figcaption></figure>

After verifying successfully, enter an **Application Name** into the field and press **Create Application:**

<figure><img src="../.gitbook/assets/2023-12-07 - 14-02-43@2x.png" alt=""><figcaption></figcaption></figure>

It'll say **Success** and then show you the application details.

Next time you can enter your already created application name and press **Load Existing App** instead.

The final configuration step: we need to enter the **Port** and **Protocol Type**.

Open your scene in Unity (for example our Tanks demo), find the NetworkManager, find the Transport component, copy the Port into the plugin.\
\=> If you are using `Kcp` then the **Protocol Type** is `UDP`.\
\=> If you are using `Telepathy`, then it's `TCP`.

<figure><img src="../.gitbook/assets/2023-12-07 - 14-05-19@2x.png" alt=""><figcaption></figcaption></figure>

**Almost done!**\
Before we can Build and push, we need to install the Linux build support for Unity, and Docker!

### Install Unity Linux Build Support

In your **Unity Hub**, select **Installs**, press the Configuration icon next to your Unity version and click **Add Modules**:

<figure><img src="../.gitbook/assets/2023-11-03 - 18-52-53@2x.png" alt=""><figcaption></figcaption></figure>

Find and install **Linux Dedicated Server Build**. You may add the others too just to be safe:

<figure><img src="../.gitbook/assets/2023-11-03 - 18-54-28@2x.png" alt=""><figcaption></figcaption></figure>

Press Continue, wait, restart Unity once, done.

### Install Docker Desktop

**Edgegap** works with containers, which means we need to install Docker. We don't need to worry about it ever, we just need to install it once.

The easiest way is to simply install **Docker Desktop**:

{% embed url="https://www.docker.com/products/docker-desktop/" %}

Download it, install it, open it and leave it running. You can pretty much auto start it with your operating system each time.

{% hint style="warning" %}
Some users reported that they had to restart their computer once before they could use Docker. To be safe, consider restarting once!
{% endhint %}

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

Next, we need to log into Edgegap's docker registry.

We are working with Edgegap to automate this. For now you need to open a Terminal / Console:

* On Windows, hit CTRL+R, enter CMD, hit enter to open it.
* On Mac, open Finder, go to Applications -> Utilities -> Terminal.
* On Linux, you probably know how to do it.

{% hint style="info" %}
Don't be scared of the Terminal. It's just black background and white text where we'll enter exactly one command. This will be automated soon.
{% endhint %}

Grab your Username + Token from the **Container Registry** page on Edgegap.com:

[https://app.edgegap.com/registry-management/repositories/list](https://app.edgegap.com/registry-management/repositories/list)

You can find them under **Credentials:**

<figure><img src="../.gitbook/assets/2023-12-07 - 14-19-45@2x.png" alt=""><figcaption></figcaption></figure>

\
Now enter this simple terminal command. There won't be any others, promised!

```
docker login registry.edgegap.com
```

Then enter your Username and Token (as password) that you see on the Edgegap.com -> **Container Registry** page.

If you can't seem to login, you can also try this command instead:

```
docker login -u "YOUR_USERNAME" registry.edgegap.com
```

Docker Desktop remembers your login, so you won't have to do this again next time.

Alright, that's it for Docker.



## Build and Push

Before we build the game server, select your `NetworkManager` and make sure that **Auto Start Server** in **Headless Mode** is enabled. Otherwise the server application may launch but not call NetworkManager.StartServer(), so no one would be able to connect.

<figure><img src="../.gitbook/assets/2023-12-07 - 14-13-06@2x.png" alt=""><figcaption></figcaption></figure>

Next, press **Build and Push** in the Unity plugin:

<figure><img src="../.gitbook/assets/2023-12-07 - 14-16-59@2x.png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Note that **Edgegap** does not see your game's source code. \
They only see a finished Docker container.
{% endhint %}

You'll see a progress bar for a while:

<figure><img src="../.gitbook/assets/2023-12-07 - 14-23-56@2x.png" alt=""><figcaption></figcaption></figure>

Unity will create a Linux build, then create a Docker build, then upload the whole thing to Edgegap. Note that uploading will take a while depending on your internet connection. The progress bar halts while uploading, but you can check your operating system's bandwidth usage to see if it's still uploading:

<figure><img src="../.gitbook/assets/2023-11-03 - 19-17-10@2x.png" alt=""><figcaption></figcaption></figure>

If this all worked, then it'll simply finish without telling you. We are working on making this more obvious.

If this failed, it'll show you errors.

Here are a few common issues and workarounds:

* **Missing Linux Build Support**: install it in your Unity hub. Make sure you do it for the Unity version that you are using in your project. This generally works once you have the Linux Build Support installed.
* **Incremental Build Failed**: delete your previous Unity Linux build in the /Builds folder next to the /Assets folder, restart Unity try again. Delete your Library/ folder if you need to. This is a Unity bug that happens sometimes.
* **Docker authorization Failed**: make sure Docker Desktop is running and make sure that you are logged in with the above Terminal command. Also make sure that image registry consists of the **Container Registry**'s 'Project' + "/" + "your-game".

If you encounter other issues, talk in our **#edgegap** Discord channel. We want to find solutions for any possible issue and explain this here!

##











































As mentioned, Edgegap gives you 0.5 vCPU for free. In other words, 50% of a virtual CPU core in the cloud. That's enough to test simple games. We hope that this increases to 1 vCPU long term.

Of course, you can always have more vCPUs if you send them money.

For now, let's start by creating an account on the Edgegap website. It's free.

{% embed url="https://app.edgegap.com/auth/register" %}

## Start Server

Once we finished building, simply press the **Start Server** button. It'll be **Deploying** for a while:

<figure><img src="../.gitbook/assets/2023-11-03 - 19-21-14@2x.png" alt=""><figcaption></figcaption></figure>

Eventually it'll be started:

<figure><img src="../.gitbook/assets/2023-11-05 - 13-01-05@2x.png" alt=""><figcaption></figcaption></figure>

This screenshot is from my other test application which uses TCP port 5000.

For you it should show you UDP port 7777, just as we configured in the Application.

Edgegap will assign you a random **External Port** that maps to your configured Port.

That is because they may have multiple deployments asking for the same port 7777.

But there's only one port 7777.

That's why they have External ports that are random.

Long story short, you may now connect your game!

## Connect your Game Client

Press **Play** in Unity, enter the **Server DNS** and **External Port** from the hosting plugin (there's a **Copy** button) and press **Client** to connect:&#x20;

<figure><img src="../.gitbook/assets/image (141).png" alt=""><figcaption></figcaption></figure>

**You should now be connected to your Deployment! 🚀**

<figure><img src="../.gitbook/assets/image (142).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
It's important to understand the magic that is happening here.\
Not only can we easily launch our game server directly from within Unity.\
We can even launch thousands of them from the Edgegap website! 🤩
{% endhint %}

## Optional: Grab the Server Status at Runtime

If you need to grab the server status from the above screenshot from your Game, you can use:

```csharp
Edgegap.Status status = Edgegap.EdgegapServerDataManager.GetServerStatus();
```

## Troubleshooting Connection Issues

If your Server Status says **Ready** but you can't seem to connect, try this:

* On the Edgegap website, go to Deployments -> select your Deployment -> select **Container Logs**, check the log files to see if your game server actually launched or if there are issues.
  * If it says "exec user process caused: no such file or directory": this can happen if you pushed an ARM build to Edgegap's x86 infrastructure. We already updated the plugin to properly cross compile from ARM so this generally should not happen anymore.
* If everything seems fine but you still can't connect, please talk to an Edgegap employee in the Mirror Discord's **#edgegap** channel.&#x20;

{% hint style="warning" %}
As mentioned, this is cutting edge technology, with lots of rough edges. You'll most certainly encounter errors and headaches for now. Please report any issues so Edgegap can fix them.
{% endhint %}

{% hint style="info" %}
We are aware that this guide was quite long.\
A lot of these steps will be automated over time.

Long term this will be a super easy way to host and scale your multiplayer game!
{% endhint %}

{% hint style="info" %}
Big thanks to our Canadian 🇨🇦 friends at **Edgegap** for giving Mirror users 0.5 vCPU for free!\
While we hope for a larger free tier in the future, please keep in mind that they are the ones who are paying for the infrastructure. If you have the means, please pay them for more vCPUs!
{% endhint %}
