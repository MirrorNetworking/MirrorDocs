# SyncDirection

![](<../../.gitbook/assets/2022-10-18 - SyncDirection.png>)

We have recently added the new **SyncDirection** feature, so here is a quick overview.

#### Why SyncDirection

Usually, data is synced from server to client in OnSerialize.

However, some components like NetworkTransform need to be able to sync from client to server, in case of client authority movement.

Previously, we had to use \[Command]s in order to sync client authoritative NetworkTransform data to the server, since OnSerialize would only go from server to client. This has several downsides:

* It's a lot of extra code to have both OnSerialize and manual remote calls
* It's extra Bandwidth: every Command includes a function hash, etc.
* Intervals need to be implemented manually, since syncInterval is only for OnSerialize.

As you can see, it would be great if there was an option for OnSerialize to go from client to server as well.&#x20;

{% hint style="info" %}
Note that ClientToServer is a bit of a simplification. Technically it goes from the owner client, to the server, where it's then broadcast to all the other observer clients which aren't owners.&#x20;

_In case of client authoritative NetworkTransform, it means your local movement is sent to the server, which then broadcasts it to the other players so they see you move as well._
{% endhint %}

Long story short, we have fully implemented SyncDirection for client authority components.

To summarize:

* **ServerToClient** is the default. **OnSerialize** (and all SyncVars, SyncLists) are sent from server to client every syncInterval. **OnDeserialize** is called on the client.
* **ClientToServer** is for client authoritative components. **OnSerialize** is called on the owner client every **syncInterval**. It's then send to the server, where **OnDeserialize** is called. Afterwards the server broadcasts to all other clients except the owner. **OnDeserialize** is then called on those other clients.

Note that ClientToServer data could still be verified in OnDeserialize on the server. That's why it's technically not 'client authority', hence the name SyncDirection. For example, while the owner client may sync NetworkTransform data to the server, you could still validate every move in the server's OnDeserialize before applying & broadcasting it.
