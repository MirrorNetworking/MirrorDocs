---
description: Custom Interest Management
---

# Custom

## Custom Interest Management

**Mirror** allows you to implement custom Interest Management solutions. For example:

* **Raycast** based so DotA players don't see other players behind a wall or in the bushes
* **Predictive Raycasting** for Counter-Strike like games. Wallhacks show players behind walls. You could create a custom interest management system to only send enemies to the player shortly before they would pop out behind a wall. In first person shooters, players move fast so you would still have to send them a couple of milliseconds before they become visible.&#x20;

All of the above custom solutions are possible in Mirror. To understand how interest management can be implemented, let's walk through it step by step.

### Script Template

Mirror includes a [**Script Template**](../general/script-templates.md) for custom interest management.  It is fully commented with all the virtual method overrides already stubbed out for you. If you used our legacy Interest Management system before, then these should look familiar.

* **OnCheckObserver** is called when someone spawns. Returns true if 'identity' can be seen by 'newObserver'
* **OnRebuildObservers** rebuilds observers for the given **Network Identity**. The result is stored in **newObservers**.&#x20;
  * Mirror will automatically put **newObservers** into **identity.observers** internally. We don't do this directly because it's a bit more complicated than adding/removing. Mirror takes care of it. Nothing to worry about :)
* **RebuildAll** is a helper function to rebuild every **spawned** Network Identity's observers.
  * Implementations probably want to call this every interval.

### **Distance** Example

Distance Interest Management is the easiest, straight forward implementation. Let's walk through it to see how to inherit from the abstract `InterestManagement` class.

```csharp
public class DistanceInterestManagement : InterestManagement
{
    [Tooltip("The maximum range that objects will be visible at.")]
    public int visRange = 10;

    [Tooltip("Rebuild all every 'rebuildInterval' seconds.")]
    public float rebuildInterval = 1;
    double lastRebuildTime;

    public override bool OnCheckObserver(NetworkIdentity identity, NetworkConnectionToClient newObserver)
    {
        return Vector3.Distance(identity.transform.position, newObserver.identity.transform.position) <= visRange;
    }

    public override void OnRebuildObservers(NetworkIdentity identity, HashSet<NetworkConnectionToClient> newObservers, bool initialize)
    {
        Vector3 position = identity.transform.position;
        
        // for each connection
        foreach (NetworkConnectionToClient conn in NetworkServer.connections.Values)
            // if authenticated and joined the world
            if (conn != null && conn.isAuthenticated && conn.identity != null)
                // check distance to our 'identity'
                if (Vector3.Distance(conn.identity.transform.position, position) < visRange)
                    // add to result
                    newObservers.Add(conn);
    }

    [ServerCallback]
    void Update()
    {
        // rebuild all spawned NetworkIdentity's observers every interval
        if (NetworkTime.time >= lastRebuildTime + rebuildInterval)
        {
            RebuildAll();
            lastRebuildTime = NetworkTime.time;
        }
    }
}
```

This isn't too complicated, is it?

* **OnCheckObserver** simply compares the distance between the newly spawned identity and a connection. A connection has a main player, which is what we use for the distance check here.
  * _Note that you could also check against every object that the connection owns. For example, if Bob spawns and Alice isn't close enough, Alice's pet might be close enough and so Alice and Bob should still see each other, despite being a bit out of normal range._
* **OnRebuildObservers**'s job is to return that HashSet of **Network Connection**s that can see our **Network Identity**. So obviously, we just iterate all **NetworkServer.connections** and check their main player's distances to our Network Identity.
  * _Note that we only check the ones that are authenticated and have a player in the world. Connections that are still logging in or choosing characters shouldn't observe anything._
* **Update**'s job is to actually call **RebuildAll()** every now and then. If we don't call **RebuildAll()**, then Mirror would never rebuild observers.&#x20;

### Host Mode Visibility

In host mode, someone runs the server while also playing on it themselves, so you might think:

* **I am the server**. The **server sees everyone**. Therefore, **I should see everyone**.

This is _technically true_, but if you were fortunate enough to ever be on a **LAN** party then you'll remember it differently:

![The best of days.](<../../.gitbook/assets/image (42).png>)

&#x20;For example, someone on a LAN hosts a Counter-Strike or DotA game. Let's think about that case for a moment:

* The **host** runs the **server**. The **server** holds the **whole world** state in memory, yet the **host player** only sees the world around him.

The idea is for the **host player** to be a regular player in the game. LAN parties wouldn't be much fun if you play DotA / Counter Strike and the host always sees everyone else's position, right?

{% hint style="info" %}
**Obviously, the host can cheat.** If you cheat on LAN then you need professional help.
{% endhint %}

Mirror has a virtual method `SetHostVisibility(NetworkIdentity, bool)` that enables / disables renderers in host mode. In other words, the world state is still there - the host player just doesn't see it.  You can override this in your custom system to suit your needs.
