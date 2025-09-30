---
description: Confused by all the hosting options? Welcome to our pragmatic hosting guide.
---

# The Pragmatic Hosting Guide

Mirror takes care of your multiplayer game code. \
However, we still need to talk about hosting!

{% hint style="success" %}
This guide is for those who want to focus on **making games**.\
Hacking commands into ssh terminals may be fun, but life is short.\
The **#1 priority** for this guide is **ease of use**, for we must ship our projects!
{% endhint %}

With a near infinite amount of providers & hosting technologies, Linux distributions and locations, it's easy to get confused about what's best for you game.

This guide will give you a basic overview. The next chapters serve as in-depth guides for the different hosting methods, depending on which you choose.

## The Pragmatic Hosting Guide

Here is a brief overview of different hosting technologies, which games they are for, and which providers you could choose from.

## Update: Edgegap Unity Plugin 🇨🇦

<figure><img src="../.gitbook/assets/image (155).png" alt=""><figcaption></figcaption></figure>

This is the new, modern way to host game servers. The Edgegap Hosting plugin is already in your Mirror folder and allows you to host to the cloud directly from Unity Editor. We wouldn't mention it here if it wouldn't make life easier.&#x20;

{% hint style="success" %}
**Edgegap** allows for ultra low latency hosting, managed directly from the Unity Editor.
{% endhint %}

Check out our step by step guide to get started: [edgegap-hosting-plugin-guide.md](edgegap-hosting-plugin-guide.md "mention")

### **1.) Hosting on your own Computer**

After building your game, you could technically host it on your own machine, or let players host it on their machines. However, this comes with several major issues:

1. **Uptime**: your computer would have to run 24/7.
2. **Security**: what if someone finds an exploit, and gets access to your private data?
3. **Performance**: your Computer / Bandwidth / latency may not be good enough.
4. **Latency**: unlike with professionally hosted servers in data centers, Player to Player connections usually come with quite some latency.
5. **Connectivity**: other players usually won't find your game behind your router, firewall, etc. player to player connections are always difficult.

{% hint style="warning" %}
This method is not recommended.\
&#xNAN;_... except for LAN parties, if you still remember those._&#x20;
{% endhint %}

### **2.) Player Hosted + Relay / NAT Punchthrough**

<img src="../.gitbook/assets/image (62).png" alt="" data-size="original">

Still, letting players host their own games can be useful. You will save money on server hosting costs and players will create their servers on demand. We just need to solve the connectivity issue. For that, we can combine two tools:

* **NAT Punchthrough**: a hacky technique to 'punch a whole' through your router / firewall so that others can connect to your game. This requires one central server for players to connect to **once**. After the initial connect to the central server, your router / firewall will most likely allow outside packets from other players directly. This works about **70% of the time**.&#x20;
* **Relay**: to avoid headaches, you can offer your own (or rent) a Relay server, which basically forwards traffic between players. Instead of talking to each other, your players all talk through a central Relay. This solves our router + firewall issues 100% of the time. However, you'll usually have to pay for bandwidth.

Note that **Uptime, Security and Latency** are still not solved here. However, a decent Relay may reduce your latency to be good enough.

{% hint style="success" %}
**Edgegap**'s [Distributed Relay](https://edgegap.com/en/platform/distributed-relay) will give you the best latency (paid).\
[**Epic Relay**](https://github.com/FakeByte/EpicOnlineTransport) is free, but with higher latency and a pretty bad C# API.
{% endhint %}

### **3.) Dedicated Servers**

![](../.gitbook/assets/hetzner.png)

This is the old school way of hosting, and probably what most people consider at first.

You sign a contract for a physical dedicated server in some datacenter, install your favorite Linux distribution, SSH into it via Terminal, configure it, then launch your server binary and maintain it over time.

Dedicated servers **work for all games**. However, they aren't ideal for all games.

It's cumbersome to sign & cancel contracts for every extra server. And you still need to pay while you don't use it.

**This is best for persistent worlds**: Minecraft, MMORPGs like World of Warcraft and so on.

{% hint style="success" %}
**Hetzner** is most often recommended. They have great prices and great hardware.\
**Namecheap** is good too.
{% endhint %}

### **4.) Cloud Hosting**

![](../.gitbook/assets/google.png)

You may have heard about Amazon AWS, Google Cloud, Microsoft Azure, etc. They offer virtual servers, which you can spin up and remove with just a few clicks without manually signing contracts & canceling them every time.

Usage is generally similar to dedicated servers: you configure your machine, pick your Linux distro, upload your server executable and maintain it yourself.

However, Cloud Hosting is **significantly more convenient**. You can rent new servers and remove old servers from a UI with just a few clicks. At the end of the month, you are billed for the resources which you have used.&#x20;

You could even configure your server once, and then spin up additional servers based on the same image with just a few clicks.

Cloud hosting **works for all games** as well. However, keep in mind that:

* Virtual CPUs are \~20% slower than dedicated CPUs.
* Pricing is noticeably higher than for dedicated servers.

You essentially pay more for extra **convenience**. New servers can be set up with just a few clicks, and once you don't need them anymore you can simply remove them. There's no need to sign contracts & wait for support all the time.

If you value convenience, then Cloud Hosting is a good choice over dedicated servers.

{% hint style="success" %}
**Google Cloud**, **Amazon AWS** and **Microsoft Azure** are the most popular choices.\
You will find hosting guides for each one in our documentation.\
At Mirror we use Google Cloud for our CCU tests & Discord Bots.
{% endhint %}

## **5.) Orchestration**

![](<../.gitbook/assets/edgegap (1).png>)

Orchestration aims to automate hosting for session based games, on demand.

If your game suddenly becomes popular, you may need hundreds or thousands of servers at a time. Setting this up manually with dedicated servers is basically impossible, and even with Cloud Hosting it would still require quite a lot of headaches & time, which is probably better spent on your game itself.

Well, good news: orchestration is the future of multiplayer game hosting. In simple terms:

1. You **create a lightweight Docker** image: configure a text file (".Dockerfile") with your Linux distribution, packages, open ports, etc. and then compile your whole project into a "Docker Image". If you aren't familiar with Docker, fear not. It makes your life a **lot** easier. Just learn it once, you'll wind up using it for all your hosting afterwards. No more manually setting up Linux servers, no more apt-get, no more maintenance etc.
2. **Upload your image** to the Orchestration Service.
3. **Configure** your orchestration in a web UI. They will ask you how many servers you want to spawn, when to spawn more, when to remove old servers, etc.

