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
&#xNAN;_&#x4E;ote that the token looks like "token 123456-abcdef-00000-..."._\
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

## Install Unity Linux Build Support

In your **Unity Hub**, select **Installs**, press the Configuration icon next to your Unity version and click **Add Modules**:

<figure><img src="../.gitbook/assets/2023-11-03 - 18-52-53@2x.png" alt=""><figcaption></figcaption></figure>

Find and install **Linux Dedicated Server Build**. You may add the others too just to be safe:

<figure><img src="../.gitbook/assets/2023-11-03 - 18-54-28@2x.png" alt=""><figcaption></figcaption></figure>

Press Continue, wait, restart Unity once, done.

## Install Docker Desktop

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

If this all worked, then you'll see a log message:

<figure><img src="../.gitbook/assets/2023-12-07 - 14-25-05@2x.png" alt=""><figcaption></figcaption></figure>

If this failed, it'll show you errors. Here are a few common issues and workarounds:

* **Missing Linux Build Support**: install it in your Unity hub. Make sure you do it for the Unity version that you are using in your project. This generally works once you have the Linux Build Support installed.
* **Incremental Build Failed**: delete your previous Unity Linux build in the /Builds folder next to the /Assets folder, restart Unity try again. Delete your Library/ folder if you need to. This is a Unity bug that happens sometimes.
* **Docker authorization Failed**: make sure Docker Desktop is running and make sure that you are logged in with the above Terminal command. Also make sure that image registry consists of the **Container Registry**'s 'Project' + "/" + "your-game".

If you encounter other issues, talk in our **#edgegap** Discord channel. We want to find solutions for any possible issue and explain this here!

## Deploying the Server

Now that we uploaded our server build to Edgegap, we need to **Deploy** (aka launch) it.

In the plugin, press **Create New Deployment:**

<figure><img src="../.gitbook/assets/2023-12-07 - 14-27-46@2x.png" alt=""><figcaption></figcaption></figure>

After a few seconds you'll see your running server in the list:

<figure><img src="../.gitbook/assets/2023-12-07 - 14-29-04@2x.png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Note that our original port was **7777,** but in the Deployment it says **30358**.\
That's because one cloud server may launch multiple game servers.\
But there's only **one** port **7777**, so it's using **Port Mapping** to find a free port for you.
{% endhint %}

## Connect your Game Client

Finally, press **Play** in the Unity Editor (or launch your client build).

Enter the deployment's **hostname** and **port** in the NetworkManager HUD:

<figure><img src="../.gitbook/assets/2023-12-07 - 14-30-36@2x.png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
Enter the hostname(fa\[...].edgegap.net) and the port (30358) separately!
{% endhint %}

Press the **Client** button to connect to it, and now you are online! [🚀](https://emojipedia.org/rocket)

<figure><img src="../.gitbook/assets/2023-12-07 - 14-34-37@2x.png" alt=""><figcaption></figcaption></figure>

{% hint style="success" %}
It's important to understand the magic that is happening here.\
Not only can you launch a game server with **Two Clicks** now.\
You can even launch **thousands of servers** with another click on **Edgegap**'s website! 🤩
{% endhint %}



To reduce costs (if you are paying), you can press **Stop Server** in the plugin once you are done.

<figure><img src="../.gitbook/assets/2023-12-07 - 14-43-38@2x.png" alt=""><figcaption></figcaption></figure>

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

{% hint style="info" %}
Big thanks to our Canadian 🇨🇦 friends at **Edgegap** for giving Mirror users 0.5 vCPU for free!\
While we hope for a larger free tier in the future, please keep in mind that they are the ones who are paying for the infrastructure. If you have the means, please pay them for more vCPUs!
{% endhint %}
