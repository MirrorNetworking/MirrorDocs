---
description: Distance Interest Management
---

# Distance

## Distance Interest Management

The straight forward, brute force solution for Interest Management is to simply send all entities to all connections within range.  This is what **Network Proximity Checker** did:

```csharp
foreach spawned entity:
    foreach connection:
        if (Vector3.Distance(spawned, connection) < visRange):
            connection.Send(spawned);
```

The only downside is that it's relatively expensive to check every entity against every connection. So if you need loads of entities or connections, it would be smart to use a faster algorithm like [**Spatial Hashing**](spatial-hashing.md).

### Setting Up

Add the **Distance Interest Management** component  to the same object as your **Network Manager**:

![](<../../.gitbook/assets/image (122).png>)

The **Vis Range** defines the radius around a player from which it receives world updates.

The **Rebuild Interval** is in seconds, and determines how often Mirror recalculates visibility of objects to clients.

### Custom Visible Range

The Vis Range setting above applies to all networked objects as the "default" range. You can override this value on objects that need a different range, e.g. exception cases for things that are very large or very small, relative to most other things in the scene. To do this, just add a **Distance Interest Management Custom Range** component to those objects and set the Vis Range values for those objects according to your needs.

![](<../../.gitbook/assets/image (20).png>)
