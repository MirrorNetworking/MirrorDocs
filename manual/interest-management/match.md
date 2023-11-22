---
description: Match Interest Management
---

# Match

## Match Interest Management

{% hint style="danger" %}
Do not use this for physics-based games...use [**Scene Interest Management**](scene.md) instead.
{% endhint %}

Match Interest Management is intended for non-physics games like card, board, arcade games.

### Setting Up

Add the **Match Interest Management** component to the same object as your **Network Manager**:

![](<../../.gitbook/assets/image (45).png>)

And add a **Network Match** component to all networked objects, including the player prefab, that will be involved in match play.

![](<../../.gitbook/assets/image (22).png>)

At runtime, assign the same `matchId` to players and objects that belong to the same match.

See the **Multiple Matches** example included with Mirror for reference and inspiration.
