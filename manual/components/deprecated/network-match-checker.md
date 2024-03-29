---
description: 'Deprecated: Use Match Interest Management instead.'
---

# Network Match Checker

{% hint style="danger" %}
**Network Match Checker is Obsolete - use** [**Network Match**](../../interest-management/match.md) **component instead.**
{% endhint %}

The Network Match Checker component controls visibility of networked objects based on match id.

![](<../../../.gitbook/assets/image (29).png>)

Any object with this component on it will only be visible to other objects in the same match.

This would be used to isolate players to their respective matches within a single game server instance.

When you create a match, generate and store, in a List for example, a new match id with `System.Guid.NewGuid();` and assign the same match id to the Network Match Checker via `GetComponent<NetworkMatchChecker>().matchId`.

Mirror's built-in Observers system will isolate SyncVar's and ClientRpc's on networked objects to only send updates to clients with the same match id.
