---
description: Documentation for our new global Interest Management system.
---

# Interest Management

## Interest Management

When making multiplayer games, the first obvious approach is to simply broadcast the world state to every player. By default, that's what **Mirror** does when you don't use any Interest Management components.

Instead of sending the full world state to every player, it's worth considering sending only what's around a player to the player. There are a few major reasons for interest management:

* **Scale**: imagine World of Warcraft. Sending the whole world to every single player would be insane. In order to scale to thousands of connections, we need to only send what's relevant to any given player.
* **Visibility**: in a MOBA game like DotA/League of Legends, not everyone should see everyone else all the time. A player should only see his own team and monsters around him. Not only that, but players shouldn't see behind walls etc.
* **Cheating**: in games like Counter-Strike, players naturally don't see enemies behind walls because the camera wouldn't render them. But if the whole world state is known in memory, then hackers could exploit that by showing players behind a wall anyway.

In other words, interest management is almost always a good idea.

### Built-in Systems

Select the **Network Manager** and add one of the built in Interest Management components.

* [**Spatial Hashing**](spatial-hashing.md) is the reason why we moved from the legacy **per-Network Identity** system to a **global** system and uses one global **Vis Range** setting that is the same for everything in the scene.
* [Hex Spatial Hashing](hex-spatial-hashing.md) is the newer, highly optimized version of the older Spatial Hashing.&#x20;
* [**Distance**](distance.md) is a parity replacement for Network Proximity Checker.
* [**Scene**](scene.md) allows for visual and physics isolation across additive scenes.
* [**Scene Distance**](scene-+-distance.md) is a combination of Scene and Distance above.
* [**Match**](match.md) isolates players for non-physics card, board, arcade games.
* [**Team**](team.md) provides for restricting visibility of networked objects to members of a team. This can also be used for owner-only items, replacing **Network Owner Checker**.
* [**Custom**](custom.md) Interest Management provides a template that you can use to create your own system.
* [**Legacy**](legacy-interest-management.md) Interest Management - Deprecated.

### Final Remarks

The new Interest Management API is fairly simple and allows for heavy customization. We walked through it with the distance based example, which is easy to understand.&#x20;

You may have noticed that this is a **global** component, yet all the functions seem to work **locally** around one **Network Identity** at a time. There are two reasons for that:

* Our **legacy** Interest Management system worked on a **per-Network Identity** basis (or **locally** if you will). For the global system, we simply moved those functions into one **global** component.&#x20;
  * This allows for global solution like **Spatial hashing**, while also guaranteeing feature pararity and easy upgrades from the old systems.
  * It's really just the old system moved to a different place. Don't fear it :)
* Interest Management can be quite complicated with all the spawning and despawning at the right time. Mirror has a whole lot of interest management code in **`NetworkServer`**, which simply calls the three functions above.&#x20;
  * The idea is to shield you from all the complexity. All you need to do is worry about **one Network Identity at a time**. That's fairly easy to think about.
