---
description: A copy of our reply to a Unity user, asking about using Unity for MMOs
---

# Unity for MMORPGs

A lot of users want to make MMORPGs with Unity / Mirror. While Unity is not the best choice for game servers, it does bring great advantages. This post was our reply to a user on the Unity forums asking about [MMOs & Unity](https://forum.unity.com/threads/mmo-server-with-unity.1410480/#post-8868246). Make sure to also check out our very old "[How to make an MMORPG](https://noobtuts.com/articles/how-to-make-a-mmorpg)" guide, which is essentially what started Mirror in the first place.\
\
\[...]

The reason why you would use Unity for the client and the server is productivity.\
Sharing physics, assets, navigation and code saves you a lot of time.\
Several smaller MMOs have been made this way, and you can target about 200-300 CCU per Unity server instance. For example, Inferna, Samutale and Naica are MMOs which were made entirely in Unity (Mirror).\
[![](https://forum.unity.com/proxy.php?image=https%3A%2F%2Fuser-images.githubusercontent.com%2F16416509%2F178149040-b54e0fa1-3c41-4925-8428-efd0526f8d44.jpg\&hash=06e9b9a127560446c7522abeb8b17e09)](https://forum.unity.com/proxy.php?image=https%3A%2F%2Fuser-images.githubusercontent.com%2F16416509%2F178149040-b54e0fa1-3c41-4925-8428-efd0526f8d44.jpg\&hash=06e9b9a127560446c7522abeb8b17e09)\
\
Note that the lower your tick rate, the more CCU you can achieve.\
For example, if your server runs at 60 Hz, that gives you 16 ms to update the whole world.\
If your server runs at 10 Hz, that gives you 100 ms.\
With 6x more time to update, you can probably have 2-3x more CCU.\
\
This is how many early MMOs achieved 500+ CCU already 15 years ago.\
Many of them ran at 10 Hz or less.\
The downside is extra latency. But hey, 500 CCU :smile:.<br>

Keep in mind that Unity is not a great choice for game servers if you care about robustness, stability, scale.\
C# standalone projects (outside of Unity) can run on netcore, which is significantly faster and more stable.\
\
Outside of Unity you also have more choices for programming languages which may be better suited for high scale / high performance servers.<br>

* Go is worth learning. A concurrent language developed by google for scalable systems. Much easier to do multithreading than with C#, due to go-routines. Also garbage collected, like C#.<br>
* Rust is great too. Bare metal C/C++ performance, but with memory safety. Multithreading is great too, because Rust protects you from data races (unlike Go and C#). Not garbage collected, so there won't be GC pauses to worry about.
* C# is still great choice. However, some of the low level networking defaults aren't great. For example, there's an ongoing discussion about C# UDP socket allocations [here](https://github.com/dotnet/runtime/issues/30797). Allocations require garbage collection, which introduces GC pauses, which introduces performance issues in high scale servers.

\
Worth noting that Unity is in the process of switching to netcore.\
That would be great for game servers!\
\
**TL;DR:**\
Unity is the slowest, most unstable solution for game servers.\
But it's also the most productive.\
\
Pick one :thumbsup:
