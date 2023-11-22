---
description: Team Interest Management
---

# Team

## Team Interest Management

Team Interest Management provides for having networked objects only visible to those on the same Team, using a Network Team component on the player object and as well as networked objects that need to have their visibility restricted to a team.

### Setting Up

Add the **Team Interest Management** component to the same object as your **Network Manager**:

![](<../../.gitbook/assets/image (58).png>)

Add a **Network Team** component to the player prefab and any other prefabs that will belong to a team.  Set the Team ID to the same value at runtime for players / objects on the same team.

![](<../../.gitbook/assets/image (35).png>)

Enable **Force Shown** on Player objects so they can be seen by players on other teams.

### Owner Only

By assigning a unique value to Team ID for each player and the objects only that client should see, you can achieve "Owner Interest Management" similar to the former Network Owner Checker.&#x20;
