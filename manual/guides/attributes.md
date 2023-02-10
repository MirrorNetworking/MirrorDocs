# Attributes

Networking attributes are added to member functions of Network Behaviour scripts, to make them run on either the client or server.

These attributes can be used for Unity game loop methods like **Start** or **Update**, as well as other implemented methods.

{% hint style="info" %}
**NOTE**: when using abstract or virtual methods the Attributes need to be applied to the override methods too.
{% endhint %}

* **\[Server]**\
  Only a server can call the method (throws a warning when called on a client).
* **\[ServerCallback]**\
  Same as **Server** but does not throw a warning when called on client.
* **\[Client]**\
  Only a Client can call the method (throws a warning when called on the server).
* **\[ClientCallback]**\
  Same as **Client** but does not throw a warning when called on server.
* **\[Command]**\
  Call this from a client to run this function on the server. Make sure to validate input etc. It's not possible to call this from a server. Use this as a wrapper around another function, if you want to call it from the server too. See also [**Remote Actions**​](communications/remote-actions.md) and [**Data Types**](data-types.md).
* **\[ClientRpc]**\
  The server uses a Remote Procedure Call (RPC) to run that function on clients. See also: [**Remote Actions**](communications/remote-actions.md)**.**
* **\[TargetRpc]**\
  This is an attribute that can be put on methods of Network Behaviour classes to allow them to be invoked on clients from a server. Unlike the ClientRpc attribute, these functions are invoked on one individual target client, not all of the ready clients. See also: [**Remote Actions**](communications/remote-actions.md).
* **\[SyncVar]**\
  [**SyncVars**](synchronization/syncvars.md) are used to synchronize a variable from the server to all clients automatically. Don't assign them from a client, it's pointless. Don't let them be null, you will get errors. You can use int, long, float, string, Vector3 etc. (all simple types) and Network Identity and game object if the game object has a Network Identity attached to it. You can use [**SyncVar Hooks**](synchronization/syncvar-hooks.md) to run code on clients when they receive updates from the server.
