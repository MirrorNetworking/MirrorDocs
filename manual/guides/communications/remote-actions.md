# Remote Actions

The network system has ways to perform actions across the network. These type of actions are sometimes called Remote Procedure Calls. There are two types of RPCs in the network system, Commands - which are called from the client and run on the server; and ClientRpc calls - which are called on the server and run on clients.

The diagram below shows the directions that remote actions take:

![](<../../../.gitbook/assets/image (96).png>)

## Commands <a href="#commands" id="commands"></a>

Commands are sent from player objects on the client to player objects on the server. For security, Commands can only be sent from YOUR player object by default, so you cannot control the objects of other players. You can bypass the authority check using `[Command(requiresAuthority = false)]`.

To make a function into a command, add the \[Command] custom attribute to it, and optionally add the “Cmd” prefix for naming convention. This function will now be run on the server when it is called on the client. Any parameters of [allowed data type](../data-types.md) will be automatically passed to the server with the command.

Commands functions should have the prefix “Cmd” and cannot be static. This is a hint when reading code that calls the command - this function is special and is not invoked locally like a normal function.

```csharp
public class Player : NetworkBehaviour
{
    // assigned in inspector
    public GameObject cubePrefab;

    void Update()
    {
        if (!isLocalPlayer) return;

        if (Input.GetKey(KeyCode.X))
            CmdDropCube();
    }

    [Command]
    void CmdDropCube()
    {
        if (cubePrefab != null)
        {
            Vector3 spawnPos = transform.position + transform.forward * 2;
            Quaternion spawnRot = transform.rotation;
            GameObject cube = Instantiate(cubePrefab, spawnPos, spawnRot);
            NetworkServer.Spawn(cube);
        }
    }
}
```

Be careful of sending commands from the client every frame! This can cause a lot of network traffic.

### Bypassing Authority

It is possible to invoke commands on non-player objects if any of the following are true:

* The object was spawned with client authority
* The object has client authority set with `NetworkIdentity.AssignClientAuthority`
* the Command has the `requiresAuthority` option set false.
  * You can include an optional `NetworkConnectionToClient sender = null` parameter in the Command method signature and Mirror will fill in the sending client for you.
  * Do not try to set a value for this optional parameter...it will be ignored.

Commands sent from these objects are run on the server instance of the object, not on the associated player object for the client.

```csharp
public enum DoorState : byte
{
    Open, Closed, Locked
}

public class Door : NetworkBehaviour
{
    [SyncVar]
    public DoorState doorState;
    
    [Client]
    void OnMouseUpAsButton()
    {
        CmdSetDoorState();
    }

    [Command(requiresAuthority = false)]
    public void CmdSetDoorState(NetworkConnectionToClient sender = null)
    {
        bool hasDoorKey = sender.identity.GetComponent<PlayerState>().hasDoorKey;
        
        if (doorState == DoorState.Open)
        {
            doorState = hasDoorKey ? DoorState.Locked : DoorState.Closed;
            return;
        }
        
        if (doorState == DoorState.Locked && hasDoorKey)
        {
            doorState = DoorState.Open;
            return;
        }
        
        if (doorState == DoorState.Closed)
            doorState = DoorState.Open;
    }
}
```

## ClientRpc Calls <a href="#clientrpc-calls" id="clientrpc-calls"></a>

ClientRpc calls are sent from objects on the server to objects on clients. They can be sent from any server object with a NetworkIdentity that has been spawned. Since the server has authority, then there no security issues with server objects being able to send these calls. To make a function into a ClientRpc call, add the \[ClientRpc] custom attribute to it, and optionally add the “Rpc” prefix for naming convention. This function will now be run on clients when it is called on the server. Any parameters of [allowed data type](../data-types.md) will automatically be passed to the clients with the ClientRpc call..

ClientRpc functions should have the prefix “Rpc” and cannot be static. This is a hint when reading code that calls the method - this function is special and is not invoked locally like a normal function.

```csharp
public class Player : NetworkBehaviour
{
    int health;

    public void TakeDamage(int amount)
    {
        if (!isServer) return;

        health -= amount;
        RpcDamage(amount);
    }

    [ClientRpc]
    public void RpcDamage(int amount)
    {
        Debug.Log("Took damage:" + amount);
    }
}
```

When running a game as a host with a local client, ClientRpc calls will be invoked on the local client even though it is in the same process as the server. So the behaviours of local and remote clients are the same for ClientRpc calls.

### Excluding Owner

ClientRpc messages are only sent to observers of an object according to its [Network Visibility](../../interest-management/). Player objects are always observers of themselves. In some cases, you may want to exclude the owner client when calling a ClientRpc. This is done with the `includeOwner` option: `[ClientRpc(includeOwner = false)]`.

## TargetRpc Calls <a href="#targetrpc-calls" id="targetrpc-calls"></a>

TargetRpc functions are called by user code on the server, and then invoked on the corresponding client object on the client of the specified `NetworkConnection`. The arguments to the RPC call are serialized across the network, so that the client function is invoked with the same values as the function on the server. These functions should begin with the prefix "Target" for naming convention and cannot be static.

**Context Matters:**

* If the first parameter of your TargetRpc method is a `NetworkConnection` then that's the connection that will receive the message regardless of context.
* If the first parameter is any other type, then the owner client of the object with the script containing your TargetRpc will receive the message.

This example shows how a client can use a Command to make a request to the server (`CmdMagic`) by including another Player's `connectionToClient` as one of the parameters of the TargetRpc invoked directly from that Command:

```csharp
public class Player : NetworkBehaviour
{
    public int health;

    [Command]
    void CmdMagic(GameObject target, int damage)
    {
        target.GetComponent<Player>().health -= damage;

        NetworkIdentity opponentIdentity = target.GetComponent<NetworkIdentity>();
        TargetDoMagic(opponentIdentity.connectionToClient, damage);
    }

    [TargetRpc]
    public void TargetDoMagic(NetworkConnectionToClient target, int damage)
    {
        // This will appear on the opponent's client, not the attacking player's
        Debug.Log($"Magic Damage = {damage}");
    }

    // Heal thyself
    [Command]
    public void CmdHealMe()
    {
        health += 10;
        TargetHealed(10);
    }

    [TargetRpc]
    public void TargetHealed(int amount)
    {
        // No NetworkConnection parameter, so it goes to owner
        Debug.Log($"Health increased by {amount}");
    }
}
```

## Arguments to Remote Actions <a href="#arguments-to-remote-actions" id="arguments-to-remote-actions"></a>

The arguments passed to commands and ClientRpc calls are serialized and sent over the network. You can use any [supported mirror type](../data-types.md).

Arguments to remote actions cannot be sub-components of game objects, such as script instances or Transforms.
