---
description: by mischa
---

# Cheats & Anticheats

### Introduction

Back in 2009-2015 Before working on Mirror & uMMORPG, I tried to learn about MMOs by reverse engineering them and selling Bots to make a living. I will share some of the lessons learned based on questions in our Discord. This article is **incomplete**, intended to give a brief introduction into the most frequently asked topics in our discord. If you want to know more, let me know.

At first we will understand Server Authority & Client Authority, which is the first major attack vector. We will also talk about authority-independent attacks and how to protect against them.

{% hint style="info" %}
As rule of thumb, never trust the client!
{% endhint %}

### Server Authority vs. Client Authority

First things first. Mirror is server authoritative by default. In other words, the server makes the all decisions. Cheaters usually modify the client to exploit games where the client is trusted with some decisions (aka client authority).

In other words, as long as you use full server authority you are fine until someone physically hacks your server machine. If you use client authority for parts of your game (like movement), then those are the parts you need to worry about.

Just to be clear, here is the difference between server & client authority explained by using a health potion

| Server Authority                | Client Authority                                 |
| ------------------------------- | ------------------------------------------------ |
| Client: may I use this potion?  | Client: I use this potion. My new health is 100! |
| Server: verifying...            | Server: ¯\\\_(ツ)\_/¯                             |
| Server: your new health is 100! |                                                  |

In practice, you need to validate any client input in `[Commands]`. Here is an [actual video](https://www.youtube.com/watch?v=C0txZCB9ZXc) of someone exploiting a game made with Mirror where the devs didn't verify client input. The game _probably_ has a `CmdSellItem` function like this:

```csharp
[Command]
void CmdSellItem(int slot, int amount)
{
    // get player's item at inventory slot
    Item item = player.inventory[slot];
    
    // sell to npc
    item.amount -= amount;
    player.gold += item.price * amount;
}
```

Notice how we blindly trust the client to send the correct amount. There is no check whatsoever. If the player only has one item, but an attacker sends 'amount = 100', we would still trust it and sell 100 items. Instead, we need to **validate any input**:

```csharp
[Command]
void CmdSellItem(int slot, int amount)
{
    // valid slot?
    if (0 <= slot && slot <= player.inventory.Count)
    {
        // get player's item at inventory slot
        Item item = player.inventory[slot];
        
        // valid amount?
        if (0 < amount && amount <= item.amount)
        {
            // sell to npc
            item.amount -= amount;
            player.gold += item.price * amount;
        }
    }
}
```

### Client Authority - the root of all Evil

#### Trusting the Client for Movement

If Mirror is fully server authoritative by default and client authority allows for cheating, then why would anyone use client authority?&#x20;

Because it's easy. A lot of games use client authority for movement at first, or forever. In server authority, the client would have to ask the server before every move. This introduces a lot of latency between pressing the key and seeing the actual movement. It's not fun at all.

In client authority mode, the player moves as soon as the key is pressed. **Instead of asking** the server to move, it tells the server that it moved. This feels great, but also introduces cheaters to **tell the server** whatever they like, e.g. "I moved here twice as fast".

Networked movement is difficult. It's possible to have fast movement that is also server authoritative (rubberbanding / prediction / etc.), but many opt not to do it at first in order to save months of development time.

#### Trusting the Client for Input

In some genres like First Person Shooters, trusting the client with some parts of the game is inevitable. In this case, aiming. Whenever we trust the client, that trust can be exploited by hackers. In FPS games, an **Aim Bot** can pretend to move the cursor onto another player more quickly. And since the server trusts the client with moving the cursor, it opens up for cheating with no easy solution.

{% hint style="info" %}
To summarize, we may _want or need to_ trust the client with some parts of our game. Those are the parts we need to protect against cheating.
{% endhint %}

### Server Authority "Cheats"

Just to be clear, even for 100% server authoritative games like MMOs, there are still attack vectors. The point of this article is to worry about the most **obvious** attacks on client authority first. Even if the server does not trust the client, there is still room for Bots which technically don't cheat, except for automating tasks that the player is supposed to do manually.&#x20;

**Bots** are tools that analyze the game state and generate input to **automatically** farm gold or kill monsters while the player isn't around. This can go to extremes where some players use hundreds of Bots to farm and then sell in-game gold for real money.&#x20;

{% hint style="info" %}
Keep in mind that server authoritative cheats are a **luxury problem**. If your MMO becomes so successful that people develop Bots, then you pretty much made it.&#x20;
{% endhint %}

Protecting against server authoritative "cheats" goes beyond the scope of inital development. There will be plenty of time to deal with those after releasing. Someone running a Bot in his basement is not a serious threat until your game, unless it gets out of hand.

And just to be clear, it's possible to **detect Bots** both on the client side and on the server side. But worry about it 5 years from now when the problem arises, not today.

### How Cheats are Made

Let's do a quick dive into how cheats are actually made.&#x20;

Your game stores a whole lot of relevant information in its memory. For example: the local player's location, other player's locations, monster locations, health, names, etc.

#### Finding Memory Locations

The majority of cheats need to read some of that information out of your game's memory. Tools like **Cheat Engine** allow you to search the game's memory for specific values. For example, if you have 100 health then you search for "100" and may find 10,000 places in memory with the value "100". But if you take a potion and increase your health to 200, then you can likely narrow it down to a handful of values that previous were "100" and now changed to "200". If you do this a couple of times, then you can usually narrow it down to one place in memory. For example, the local player's health might be stored at the memory address `0xAABBCCDD`.

But there is one **problem**: next time we start the game, the game will set up the world again and your player's health is most certainly not at the same memory address anymore. Tools like Cheat Engine allow you to **"find what accesses..."** that memory location by setting breakpoints. Use a potion again, the breakpoint fires and now you know which part of your game accesses that memory location.

Instead of just `Health`, you now have `Player->Health` (this is a simplification, in practice you go from `0xAABBCCDD` to a pointer with an offset like `[0x00FF00FF+0x8]` where 0x00FF00FF is the location of your player object in memory, and `0x8` is the offset for `Player->Health`. It's likely that `Player->Mana` would be at `+0x12`, or at the next place in memory. This process can be repeated until you have `Game->Player->Health`where `Game` is finally an address relative to the program's entry point.

In other words, we can now always read the Player's health even after restarting the game.

This process can be repeated for inventory, skills, monsters, positions, etc. The more information we can find, the easier it is to write cheats.

If our game uses **client authority** then we can actually modify the Player's Health in memory! If we use server authority, then we can still modify it in memory but the change is only visible on this client. The server doesn't trust the client with health, and the next time it syncs the new health to the client, the value in memory will be overwritten again.

{% hint style="success" %}
This is how Mirror's **\[SyncVar]** works! You can modify them in Cheat Engine, but nobody cares because they are server authoritative.
{% endhint %}

#### Making it harder to find Memory Locations

The process of finding memory locations via pointers & offsets is cumbersome. Whenever the game changes, the offsets change too. For example, if previously we had

```csharp
class Player
{
    int Level; // at +0 in memory
    int Health; // at +4 in memory
    int Mana; // at +8 in memory
}
```

And the game is changed to:

```csharp
class Player
{
    int Class; // at +0 in memory
    int Level; // NOW at +4 in memory
    int Health; // NOW at +8 in memory
    int Mana; // NOW at +12 in memory
}
```

Then the cheat developer would have to manually search for all offsets in memory again. This is somewhat painful to deal with.&#x20;

{% hint style="success" %}
Messing with the memory layout occasionally is a nice way to make reverse engineering more difficult. Protecting against reverse engineering is a function of **return vs. effort**. Nobody will spend 10 hours a day reverse engineering if the hack only ends up making $10/month. The harder we make it, the less it'll be worth it.
{% endhint %}

#### Projecting Memory Values

Here is a fun little technique that can actually be done in any game, without much risk. Instead of storing Health directly, we could store a projected value like:

```csharp
struct AntiCheatInt
{
    int projected;
    public int Value
    {
        get => projected + 1;
        set => projected = value - 1;
    }
}
```

This is a simplified example, but the idea is to not store our "100" health directly in memory. Instead we store the value modified by one, or by more complex projections. This already makes the whole **Cheat Engine** initial finding process really depressing while adding virtually no risk. Nothing can really go wrong if you do this in Unity.&#x20;

{% hint style="success" %}
Projecting Memory values is an easy way to make cheat development more annoying. Note that there is a minor performance impact and note that this is only useful if you use IL2CPP in Unity.&#x20;
{% endhint %}

{% hint style="info" %}
When protecting against cheats, there is a **fine balance** between making cheater's life harder while not annoying honest players. Some techniques like UPX packing (see below) have a high probability of annoying everyone. Other techniques like Projecting Memory have a low probability of annoying anyone.
{% endhint %}

#### Making it harder to access Memory

There are various techniques to make reverse engineering more painful to begin with. For example:

* **Fake entry points** that change dynamically, e.g. with exe packing like **UPX packer**. Those aren't too hard to unpack, but it adds difficulty.
  * _Note that UPX packed executables are often flagged as viruses._
* **Detecting Debuggers** like Cheat Engine/MHS via **IsDebuggerPresent**. Note that this is pretty easy to work around because everyone knows IsDebuggerPresent arleady. More advanced techniques might involve tricks like measuring time between instructions. For example, if we measure a simple integer multiplication with a **StopWatch at runtime** and it ends up taking several milliseconds, then someone is most likely stepping through this code with a debugger.
* **Virtualization** with tools like **Themida** or **Enigma Packer** is the holy grail when it comes to protecting against reverse engineering. If finding memory locations in regular processes is hard, then finding them in processes inside of virtual machines is orders of magnitude harder. Back when we used to reverse engineer games, we would never touch virtualized processes simply because the effort vs. reward would not ever be worth it. Nobody is going to spend half a year analyzing your virtual machine instructions, unless your game is as huge as World of Warcraft.
  * _Note that virtualized executables are often flagged as viruses. You would need a custom virtualization engine that is not flagged as a virus._

{% hint style="info" %}
**Note** that many of those techniques can be risky in **Unity,** which already introduces several layers of complexity by going from **C#**->**IL**(->**IL2CPP**->**Assembly**). The probability of messed up entry points breaking something somewhere is high. As rule of thumb, use **IL2CPP** in any case, since it changes the game from IL to Assembly, which is way harder to reverse engineer already. If cheating becomes a serious problem, consider Virtualization.
{% endhint %}

Now that we understand how cheats are developed, we can take a look at how some common cheats work and how to protect against them.

### Ollydbg/IDA/Code Caves

Let's say your game has a function like:

```csharp
void SetHealth(int health)
{
    this.health = health;
}
```

Which might produce _(simplified)_ assembly code like:

```csharp
...
mov edi, eax // edi is this.health, eax is the new value
...
```

Hackers can use advanced debugging tools to modify your game's assembly to:

```csharp
...
mov edi, 100 // always sets health to 100
...
```

Instead of searching and modifying memory values with Cheat Engine, it's possible to **modify the game's own assembly** code directly.&#x20;

Modifying the game's assembly can be extremely powerful for developing hacks. **Code Caves** are often used to inject custom functions into the game, for example:

```csharp
...
JMP 4 // jump to our custom code
...
mov edi, eax // do the original thing
... custom code ... // do whatever we want
JMP 2 // jump back to the original function
...
```

In C#, this would be equivalent to the user injecting his own code into our Health function like:

```csharp
void SetHealth(int health)
{
    CodeCave(health);
}

void CodeCave(int health)
{
    this.health = health;
    // do all kinds of magic here
    // for example, if health==0 then call the code
    // that clicks on the Respawn button to respawn
    // automatically.
}
```

This is a simplified example, but a very common technique to know about. To protect against custom assembly, it might be smart to generate checksums of your exe files.

### Wall Hacks / ESP

In first person shooters, wall hacks are one of the most common cheats. People can modify your executable to show players behind walls. This is reasonably easy to do and pretty common.&#x20;

To protect against it:

* Make reverse engineering as difficult as possible (see above chapter)
* Use Mirror's **Interest Management** to not display far away players. You could implement raycast based Interest Management where players are only sent to clients if they are actually seen.&#x20;
  * _Note that you would want some kind of tolerance to send them early enough, e.g. send 1s before they are seen. This is not perfect, but it's better than allowing players to see all other players all the time. Interest Management is huge for this._
* Detect Wall Hacks at runtime and ban cheaters using them.&#x20;

This is a hard problem, even popular games like Counter-Strike have a really hard time dealing with this. It's a constant battle.

### **Speed Hacks**

If you made the choice to use client authoritative movement becuase it's easier, then you will most likely encounter speed hacks in your game eventually. Speed hacks can be implemented in various ways, ranging from simply modifying `Player.Speed` in memory, to messing with the computer's clock speed which is pretty hard to work around from Unity.&#x20;

To protect against it:

* Check the movement speed on the server. Allow for some tolerance for network conditions. Many games allow 10-15% tolerance, but anything higher than that is most likely a speed hack.

### Bots

As mentioned before, Bots are particularly evil since they don't requires any _real_ cheats or client authority. Additionally, they can mess up your game's economy and make it simply not fun to player if everyone around you is a Bot.

To protect against it:

* Make finding memory locations hard. See chapters above.
* Adjust your memory layout occasionally. Add unnecessary values between `Player.Health` and `Player.Mana` sometimes.
* Adjust your network protocol occasionally. The most advanced Bots don't even need to read your memory. They use work with your game's send/recv functions directly. Modify your NetworkMessage opcodes and layout occasionally and you will make reverse engineering really painful.
* Detect known Bot.exe processes by checksum, name, etc.&#x20;
  * _Note that this too often flags your game as a virus. Games shouldn't look for running processes._
* Detect Bot **Patterns** on the server. This is how I would do it if Bots become a serious problem.
  * In the simplest form, if someone is playing 24/7 for a week on end then it's probably a Bot, or in rare cases some guy in an internet cafe.
  * If the player always uses the same path or levels at the same spot all the time, it's probably a Bot.
* Add a **Report button** in your game. Look into reported players. Try to talk to them and see if they respond, etc.
* Spawn **Honeypot Monsters** in places of high activity. If a certain area has a whole lot of monster kills over a period of time, spawn a obviously different looking but extremely strong monster in that area. Regular players would notice and move elsewhere for a while. Bots would hit it and die.&#x20;

Again, those are simplified answers to a complex problem. If your game becomes successful, it will be a constant battle. Which is fine, as long as you know that you are even fighting a battle.

### Silent, Delayed Detection

One of the biggest mistakes games make is to let the user know when a cheat or reverse engineering tool was detected. All it does is let the reverse engineer know where to look in the code in order to disable the checks.&#x20;

If we go through all the work of detecting cheats and debuggers, we should keep quiet and use the information to our advantage. Instead of loudly announcing a cheat attempt, quietly send some info to the server. Flag the player in a database.&#x20;

Do **not** immediately ban or kick anyone. It's smarter to wait a random amount of time.

* Users might try multiple cheats with different versions over the month.
* Reverse Engineers might use different tools and modify the game in different ways.

If we only ban people once a month, then it's not obvious at all what caused the ban. It will introduce **huge turnaround** times to test which cheats get detected and which don't.

{% hint style="success" %}
Silent Detection is the most powerful tool we have to win the fight against cheaters. Use time and information to your advantage.
{% endhint %}

### Free to Play vs. Paid Games

Here is one final consideration which I'll most likely also do for my own game. While free 2 play games are great to attracts huge amounts of players, there might be value in paid games if you are just a small indie developer not ready to deal with hordes of fake accounts and hackers.

People having to pay a one time price to play your multiplayer game introduces a huge hurdle where hackers and cheaters would have to buy your game again if they got banned. Additionally it adds some level of verification to make sure people can't just create accounts over and over again. You could ban credit cards etc. if needed.

### Summary

To summarize, cheating is a complex topic and there will never be a final solution. Imho make everything you can server authoritative. For movement, at least aim to make it server authoritative at some point, e.g. after releasing your game when you start seeing the first speed hacks or when you actually have some breathing room.&#x20;

Once your game becomes successful, probably someone to fight the battle. There's plenty you can do to make it harder.&#x20;

Ultimately, it's function of effort vs. reward. The more annoying you make it to cheat, the more likely it is that people won't bother or will just move to easier targets.

This topic could fill a whole book, but I hope you learned a few of the basics from this.
