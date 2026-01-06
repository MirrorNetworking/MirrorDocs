---
description: >-
  Another post about cheat protection, copied from the Unity forums for future
  reference.
---

# Cheat Protection Stages

**Source:** [https://forum.unity.com/threads/help-me-choose-networking-solution-for-formation-tactics-game.1340309/](https://forum.unity.com/threads/help-me-choose-networking-solution-for-formation-tactics-game.1340309/)



\[Cheat Protection] is a broad topic, with different stages of protections that you should be aware of.

### **Server Authority**

\
It would be wise to implement almost all game logic with **server authority** first, before worrying about client sided anti cheat.\
\
For example, if a player's **health** is client authoritative then no amount of client sided anti cheat will protect you from hackers attaining god mode. Where as with server authority, you would not ever ask the client about the current health. Instead, the server would tell the client about its health and make all decisions related to health, and validate all of the client's requests related to health. For example, when requesting to use a potion, always ensure the player actually owns that potion, is not dead, etc.\
\
This essential makes health unhackable, unless you are able to access the server machine.\
\
However, certain aspects like **movement** can be quite hard to make both responsive and secure.\
It's certainly possible to implement all of your game state with server authority. Even for movement, where you might move the client immediately, send the movement to the server, validate if the move was legal, and then accept / reject it.

### **Minimizing client information**

\
That being said, clients may still gain advantages by simply reading state from memory without manipulating it.\
For example, if your tactics game accidentally keeps all of the enemy's positions in memory, then hackers may attempt to extract it for their advantage.\
\
In this case, it would help to only send **relevant information** to the client. For example, many netlibs provide customizable interest management. You could implement Raycasting with some tolerance in order to check if a player sees another player's units. Otherwise, don't even send it to the player at all (in which case it can't be extracted from memory).

### &#x20;**Client sided anti-cheat**

\
Lastly, even if your game is server authoritative and minimizes information sent to the client, there may still be cheats. For example, auto clicks, farm bots, aim bots and such.\
\
This is when you should start worrying about client sided anti cheat.\
But keep in mind that this is a catch & mouse game that you will not win, only delay.\
Reverse engineers have sophisticated tools to circumvent client sided anti cheats, and you will most likely not be able to compete with them all on your own.\
\
It gets so bad that some popular games need to move their anti cheat software out of userspace into the kernel, where it's harder for hackers to circumvent it. If your game is popular enough, it will be circumvented eventually.\
\
This is a very broad topic and there are lots of things you can do that I haven't mentioned, like obfuscation, virtualization, etc. But as mentioned, it would be wise to start with server authority & minimizing information sent to the client ![:)](https://forum.unity.com/styles/default/xenforo/clear.png)\
\
On a side note, imho attempting to protect client sided hacks isn't ideal.\
Instead you should try to silently detect hacks, and then ban hackers after random amounts of time. This way a hacker would not know which particular action was detected.<br>

### &#x20;**Anti Cheat with Mirror**

\
Note that Mirror is server authoritative by default.\
Interest Management & SyncMode may be used to minimize information sent to clients.
