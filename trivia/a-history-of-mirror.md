---
description: Quick overview of why Mirror exists, and how we got here - by vis2k
---

# A Brief History of Mirror

Our community has grown a lot. Most of you weren't around when this all started, so here's a brief history of UNET/Mirror.

Hopefully an entertaining read if you are new to Mirror. Perhaps a trip down memory lane with some nostalgia screenshots for those who've been around all this time.

{% hint style="info" %}
We will keep updating this article as Mirror develops over time.
{% endhint %}

## UNET

![](<../.gitbook/assets/image (49).png>)

Back in summer 2015 Unity released a public beta of **UNET**. The idea of Unity worrying about networking so we could worry about our games sounded very tempting. UNET came with several interesting features:

* **Server & Client in one project**. Most of the code is shared. Some is marked as \[Server] or \[Client] only.
  * This allows for a major gain productivity since terrain, models, assets and code are all shared between server & client.
* **\[SyncVars]** for automated serialization of selected variables.
  * This was significant when coming from hand built Serialize/Deserialize functions. Simply adding a \[SyncVar] in front of Player. level was so much easier.
* **\[Commands/Rpcs]** - wrapping a function with a \[Command] tag to automatically call it on the server was another huge gain productivity
  * Compared to manually sending a message, deserializing all parameters and calling a function manually.

## Hype

Back in 2015, there seemed to be almost monthly UNET talks and videos like [Democratizing Multiplayer Development](https://www.youtube.com/watch?v=gZbbYXzyXKk). Everyone was excited, and the UNET team was working hard.

![Sean Riley demonstrating UNET at Unite 2014](../.gitbook/assets/2021-04-02\_18-35-53@2x.png)

![One of the early UNET talks in 2015](../.gitbook/assets/2021-04-02\_12-36-24@2x.png)

![Alexey Abramychev's talk about LLAPI](../.gitbook/assets/2021-04-02\_18-45-38@2x.png)

Back then, it was mostly:

* **Alexey Abramychev**: the friendly Russian guy who worked on the low level Transport (LLAPI)
* **Sean Riley**: who worked on World of Warcraft, now developing the high level API (HLAPI/UNET)
* **Erik Juhl:** the UNET team lead, who said _MMOs were his passion_

The UNET team had a big plans on their **roadmap**:

* Cloud services like Relay/Matchmaking
* One-click game server hosting right from the Unity Editor
* The _simulation server_ which will power MMO scale networking
* UNET 'Phase 3' aka automated server instances. Mark an area, it'll run as an instance.

Most multiplayer games never see the light of day, because finishing all that code is extremely difficult. With UNET, for the first time it seemed actually doable. An order of magnitude gain productivity for sure.

## Getting Started

UNET was still in beta in 2015. There wasn't much documentation, but there were just a couple of demos. One of them was a movement demo with a couple of balls moving over the network.

I was already working on my MMO project before UNET. Moving balls sounded close enough to moving players, so I gave UNET a try.

![UNET's movement demo with some early MMO features](../.gitbook/assets/2015-09-30\_unet\_noicons.png)

**Networked** NavMeshAgent movement, skills, equipment and an inventory were added easily thanks to SyncLists and all the \[SyncVar]/\[Rpc] convenience.

UNET wasn't very stable during the beta, but the productivity gain was massive and it seemed like the obvious path into the future of multiplayer game development.

## Meet Damon

Around September I also started talking to Damon Slye, the guy who made [Red Baron](https://store.steampowered.com/app/263940/Red\_Baron\_Pack/) in 1990, published by Sierra.

![Red Baron](<../.gitbook/assets/image (85).png>)

Damon and his new team Mad Otter Games actually made their own indie MMO, just like I planned to do. They didn't use Unity, but considered it for their next project. We've talked a lot about MMO tech, lessons learned and they even licensed me a couple of their models for my unnamed MMO project.

With the UNET team working tirelessly on networking and the MadOtter guys as living proof, it seemed like my indie MMO idea actually seemed almost doable for once.

## What if...

![The MMO Project in November 2015 with the licensed models.](../.gitbook/assets/2021-04-02\_13-00-01@2x.png)

In 2015, there wasn't any type of MMO toolkit available as far as I could tell. From my reverse engineering days I remembered Perfect World Entertainment licensing their MMO engine for $100,000 - $300,000. Which needless to say was out of my budget.

Since I was working on my own MMO code anyway, I figured might as well put it on the Asset Store and see what happens. Maybe it sells for $50/month and I can treat myself to a pizza every week or so.

[uMMORPG](https://assetstore.unity.com/packages/templates/systems/ummorpg-remastered-159401) was released on the Asset Store at December 23rd, 2015. One day before Christmas.

![An early version of uMMORPG](<../.gitbook/assets/2017-01-11\_10-42-44 (1).png>)

As it turns out, a lot of people had the same dream, but not enough money to license a AAA engine.

uMMORPG quickly became one of the top 10 assets on the store.

Yesterday, MMO development was my hobby, a dream to work on my free time. Over night it became a full time job, thanks to the Unity Engine, UNET and a huge bet.

## In UNET We Trust

Around that time, **Paul** and I crossed paths when he used uMMORPG for his Cubica game.

There was just one problem: UNET. We've put all our trust into a networking black box and a couple of professional networking engineers.

Yet, even after 30-50 bug reports and 1-2 years of begging every single person on the UNET team - nothing happened. Not a single bug was fixed. No improvements. No answers.

Every single day our users/customers came in with new UNET bugs. Bad reviews. Random crashes. We've built our dreams on a blackbox that abandoned us, and there was nothing we could do about it.

Worst of all, UNET didn't scale. 30-70 CCU and a dozen monsters were all it could do.

![An early UNET community test with 57 connections and lots of bugs.](<../.gitbook/assets/57 (1).png>)

At one point, I used reflection to fix the serialization bug in UNET. At the time, it was the highest rated networking bug on the Unity issue tracker. I've made detailed forum posts on why the bug happens and how to fix it. I reached out to Alexey, Erik, Larus, the Asset Store team, and QA.

It was closed _by design_. Twice.

!["By Design" - the UNET Bug that started Mirror](../.gitbook/assets/2021-04-02\_18-39-23@2x.png)

We've built our dreams on UNET, and in my case my livelihood. I needed UNET to pay the rent and eat. Yet the people we've put our full trust in had abandoned us.

* **Sean Riley** was the driving force behind UNET. As it turns out, he left the team around the beta.
* **Alexey** tried to fill the void as best as he could. For a while, he was on the forum on Sundays and even replied to my email during his vacation. At some point, he wasn't with Unity anymore either though.
* **Erik Juhl** (the UNET team lead) went on to work for a web development company.

What's worse is that while everyone secretly left the sinking ship, there was no word from Unity. Dead silence. It took a couple of years for us to learn that the UNET core team wasn't even working for Unity anymore.

At one point we even wrote an open letter to Joachim Ante. Desperate times.

## HLAPI Pro

In a surprising turn of events, someone at Unity **open sourced HLAPI** - the high level part of UNET.

**LLAPI** - the low level transport - always remained closed source.

For HLAPI, one had to compile two csharp DLLs, manually overwrite them in their Unity installation and then pray that it was compatible with that particular Unity version.

The code was uncommented, over engineered, full of bugs and full of magic.

But we had the source. Finally, a light at the end of the tunnel.

So I made this forum post about "**HLAPI Pro - a drop in replacement for UNET**". Fully compatible, just with a couple of bug fixes that I previously had to use reflection for. The idea was to offer people a 100% risk free alternative. Use HLAPI Pro to fix your bugs. Go back to the original DLLs any time.

As far as I remember, Paul actually worked on MLAPI with TwoTen at first. But he convinced me to **MIT** License HLAPI Pro, and so Mirror was born.

{% hint style="info" %}
**Fun fact**: over the years we never really figured out the elephant in the room. How is that Unity pulled off this complex UNET technology, but then couldn't fix it? The weaver is a true piece of alien technology, and it's still not obvious to us who made that thing.\
\
When digging through it, at one point we found code for **NetworkView**. That component was actually part of Unity's legacy Raknet networking. As far as we can tell, UNET was based on their legacy system.
{% endhint %}

## Meet Mirror

During the next couple of years, we fixed several hundred UNET bugs and cleaned up the code.

Still, nobody believed in UNET. Supposedly, the design didn't make sense, it would never scale and it would never work for any real game. The only people who believed in Mirror were those already using UNET for their projects, desperately needing bug fixes.

![Early Mirror stress test with hundreds of monsters](../.gitbook/assets/2017-08-13\_stresstest.png)

At one point, we asked Unity for a donation seeing how we carried their whole UNET community for free. They told us UNET wouldn't have a future and it makes no sense to invest into that technology anymore.

Our networking heroes had abandoned us, and told us our work was pointless.

## Breaking Chains

Step by step, we broke all the chains that still tied us into UNET.

* Someone open sourced the **Weaver** after a while, so we could fix it too.
* We replaced the **LLAPI** black box with Telepathy. We've got heavy criticism for using TCP. But at least, the black box was finally open and we could fix bugs for once. Over the years, we've added many other transports like KCP.
* Paul figured out how to use **HLAPI** as source drop in, without DLL replacements.
* **Websockets** were previously closed in LLAPI, now finally open too.
* The UNET **Community** mostly converted to Mirror too.

Until today, most of our work on Mirror was about breaking the chains and fixing all the bugs.

Here are some screenshots that we gathered over the years. Enjoy the nostalgia.

![UNET Weaver Project / DLL](../.gitbook/assets/2017-08-05\_patreon\_header.png)

![Mirror's first example](../.gitbook/assets/2018-09-25\_movedemo.png)

![HLAPI's first community test](<../.gitbook/assets/Screenshot at Jul 28 13-09-17.png>)

![Geno Online, jagatai's early MMO attempt with Mirror](../.gitbook/assets/2017-09-08\_jagatai\_stream.png)

![uMMORPG 500 CCU test. UNET handled \~70 CCU when we started.](../.gitbook/assets/2021-04-02\_17-38-22@2x.png)

![20,000 monsters in Mirror. UNET handled 12 monsters back then.](<../.gitbook/assets/2020-10-12\_20-45-17 - x branch 20k monsters 19 FPS.png>)

## Community

It's been 6 years since the UNET beta. Mirror has grown to 100,000 downloads a year, a fantastic Discord community with 10,000 users, new team members like our famous networking legend MrGadget, Coburn, James, katori, Lymdum, uwee and many more.

![](../.gitbook/assets/2021-04-02\_17-52-33@2x.png)

Several [popular games](https://mirror-networking.com/showcase/) made with Mirror, and of course lots of [memes](https://www.youtube.com/watch?v=jNTFFYhNhiI).

![Population: ONE - made with Mirror](<../.gitbook/assets/image (69).png>)

## What's Next

We've spent all this time breaking the chains and fixing the bugs. Hundreds of them.

But we also learned a lot about networking since then.

In the future, our [roadmap](https://trello.com/b/fgAE7Tud) is not so much about fixes anymore, but about core networking techniques.

While stable, Mirror still has a long way to go and the original mission has never changed.

We just need it for our games.

{% hint style="success" %}
**Mirror** required 10,000+ man hours on top of UNET.\
**UNET** was developed over several years, likely on top of Unity's **legacy** networking.\
**Legacy** networking must have been developed for years by people we don't even know.\
\
Mirror is standing on the **shoulders of giants**.\
It's hard to estimate how many years, man hours, sweat, tears went into this technology.
{% endhint %}
