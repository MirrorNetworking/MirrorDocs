# Migration Guide

## Migrating a project from UNet (HLAPI) <a href="#migrating-a-project-from-unet-hlapi" id="migrating-a-project-from-unet-hlapi"></a>

This guide gives you a step by step instruction for migrating your project from UNET to Mirror. Mirror is a fork of UNET. As such the migration is straight forward for most projects.

You should review the information on the [Deprecations](deprecations.md) page to see if your project will be impacted.

There's also a [Migration Tool](https://github.com/Lymdun/MirrorConverter/) you can try.

### 1. BACKUP <a href="#1-backup" id="1-backup"></a>

You have been warned.

### 2. Install Mirror and Restart Unity <a href="#2-install-mirror-and-restart-unity" id="2-install-mirror-and-restart-unity"></a>

Get Mirror from the [Asset Store](https://assetstore.unity.com/packages/tools/network/mirror-129321) and import it in your project.

Alternatively you can grab the latest [release](https://github.com/vis2k/Mirror/releases) from GitHub if you're feeling adventurous, but be aware that bleeding edge dev releases are not necessarily stable.

**NOTE:** You must restart Unity after adding Mirror to the project for the components menu to update correctly.

### 3. Replace namespace <a href="#3-replace-namespace" id="3-replace-namespace"></a>

Replace `UnityEngine.Networking` for `Mirror` everywhere in your project. For example, if you have this:

```csharp
using UnityEngine.Networking;

public class Player : NetworkBehaviour
{
    ...
}
```

replace it with:

```csharp
using Mirror;

public class Player : NetworkBehaviour
{
    ...
}
```

At this point, you might get some compilation errors. Don't panic, these are easy to fix. Keep going...

### 4. Replace playerController with identity <a href="#4-replace-playercontroller-with-identity" id="4-replace-playercontroller-with-identity"></a>

Replace references to `NetworkConnection.playerController` with `NetworkConnection.identity`.&#x20;

### 5. Remove NetworkSettings <a href="#5-remove-networksettings" id="5-remove-networksettings"></a>

NetworkSettings in UNet have channels, but this is flat out broken. Rather than ignoring your settings we removed channels from NetworkSettings completely. `sendInterval` is now set in code and / or the inspector too.

For example, if you have this code:

```csharp
[NetworkSettings(channel=1,sendInterval=0.05f)]
public class NetStreamer : NetworkBehaviour
{
    ...
}
```

replace it with:

```csharp
public class NetStreamer : NetworkBehaviour
{
    void Start()
    {
        syncInterval = 0.05f;
    }
}
```

Please note that the default transport [Telepathy](https://mirror-networking.com/docs/Articles/Transports/Telepathy.html), completely ignores channels, all messages are reliable, sequenced and fragmented. They just work with no fuss. If you want to take advantage of unreliable channels try a UDP or Steam [transport](../transports/) instead.

### 6. Change SyncListStruct to SyncList <a href="#6-change-syncliststruct-to-synclist" id="6-change-syncliststruct-to-synclist"></a>

There is a bug in the original UNet Weaver that makes it mess with our `Mirror.SyncListStruct` without checking the namespace. In Mirror, we fixed SyncLists so that they work with structs by default.

For example, if you have definitions like:

```csharp
public class SyncListQuest : SyncListStruct {}
```

replace them with:

```csharp
public class SyncListQuest : SyncList {}
```

### 7. Replace NetworkHash128 and NetworkInstanceId <a href="#7-replace-networkhash128-and-networkinstanceid" id="7-replace-networkhash128-and-networkinstanceid"></a>

These have been changed to System.Guid and uint, respectively.

For example, if you have something like this:

```csharp
public sealed class SpawnItemMessage : MessageBase
{
    public NetworkHash128 assetID;
    public NetworkInstanceId networkInstanceID;
    public Vector3 position;
    public Quaternion rotation;
}
```

replace with:

```csharp
public sealed struct SpawnItemMessage : NetworkMessage
{
    public System.Guid assetID;
    public uint networkInstanceID;
    public Vector3 position;
    public Quaternion rotation;
}
```

### 8. Update your SyncList callbacks <a href="#8-update-your-synclist-callbacks" id="8-update-your-synclist-callbacks"></a>

In UNet, SyncLists have a callback delegate that gets called in the client whenever the list is updated. We have changed the callback to be a C# event instead and we also pass the item that was updated/removed.

For example, if you have this code:

```csharp
using UnityEngine;
using UnityEngine.Networking;

public  class MyBehaviour : NetworkBehaviour
{
    public SyncListInt m_ints = new SyncListInt();

    private void OnIntChanged(SyncListInt.Operation op, int index)
    {
        Debug.Log("list changed " + op);
    }

    public override void OnStartClient()
    {
        m_ints.Callback = OnIntChanged;
    }
}
```

replace it with:

```csharp
using UnityEngine;
using Mirror;

public  class MyBehaviour : NetworkBehaviour
{
    public SyncListInt m_ints = new SyncListInt();

    private void OnIntChanged(SyncListInt.Operation op, int index, int oldItem, int newItem)
    {
        Debug.Log("list changed " + op + " old item: " + item + " new item: " + newItem);
    }

    public override void OnStartClient()
    {
        m_ints.Callback += OnIntChanged;
    }
}
```

Notice the callback will also work in the server in Mirror.

### 9. Replace Components <a href="#9-replace-components" id="9-replace-components"></a>

Every networked prefab and scene object needs to be adjusted. They will be using `NetworkIdentity` from Unet, and you need to replace that component with `NetworkIdentity` from Mirror. You may be using other network components, such as `NetworkAnimator` or `NetworkTransform`. All components from Unet should be replaced with their corresponding component from Mirror.

Note that if you remove and add a NetworkIdentity, you will need to reassign it in any component that was referencing it.

### 10. Update Extended Components <a href="#10-update-extended-components" id="10-update-extended-components"></a>

Some commonly extended components, such as NetworkManager, have changed method parameters in Mirror. A commonly used override is OnServerAddPlayer. Using the original HLAPI, your override may have looked like this:

```csharp
public override void OnServerAddPlayer(NetworkConnection conn, short playerControllerId, NetworkReader extraMessageReader)
{
    base.OnServerAddPlayer(conn, playerControllerId, extraMessageReader);
    // your code
}
```

In your newly Mirror-capable NetworkManager, if you are using the `OnServerAddPlayer` override, remove the "playerControllerId" and "extraMessageReader" parameters from your override and the base call:

```csharp
public override void OnServerAddPlayer(NetworkConnection conn)
{
    base.OnServerAddPlayer(conn);
    // your code
}
```

See Custom [Player Spawn Guide](../guides/gameobjects/custom-character-spawning.md) for details on how to submit custom characters now.

### 11. Pick your transport <a href="#11-pick-your-transport" id="11-pick-your-transport"></a>

You can choose one of several transports in Mirror. Open your NetworkManager gameobject, in the inspector you will see a `TelepathyTransport` component by default. Drag in one of the available transports and remove `TelepathyTransport` if you wish to use a UDP based transport instead.

### 12. Configure address and port <a href="#12-configure-address-and-port" id="12-configure-address-and-port"></a>

In HLAPI, you configure the port and local address in the NetworkManager. One of our goals is to make Mirror transport independent. Not all transports need address and port. Some transports might even use more than one port at the same time, so these settings were inadequate. We removed the port and address and all other Network Info properties from NetworkManager, and we moved them to the transport components instead.

### 13. Update your firewall and router <a href="#13-update-your-firewall-and-router" id="13-update-your-firewall-and-router"></a>

LLAPI uses UDP. Mirror uses TCP by default. This means you may need to change your router port forwarding and firewall rules in your machine to expose the TCP port instead of UDP. This highly depends on your router and operating system.

## Video version <a href="#video-version" id="video-version"></a>

See for yourself how uMMORPG was migrated to Mirror:

[http://www.youtube.com/watch?v=LF9rTSS3rlI](http://www.youtube.com/watch?v=LF9rTSS3rlI)

## Possible Error Messages <a href="#possible-error-messages" id="possible-error-messages"></a>

* TypeLoadException: A type load exception has occurred. - happens if you still have SyncListStruct instead of SyncListSTRUCT in your project.
* NullPointerException: The most likely cause is that you replaced NetworkIdentities or other components but you had them assigned somewhere. Reassign those references.
*   `error CS0246: The type or namespace name 'UnityWebRequest' could not be found. Are you missing 'UnityEngine.Networking' using directive?`

    Add this to the top of your script:

    ```
    using UnityWebRequest = UnityEngine.Networking.UnityWebRequest;
    ```

    `UnityWebRequest` is not part of UNet or Mirror, but it is in the same namespace as UNet. Changing the namespace to Mirror caused your script not to find UnityWebRequest. The same applies for `WWW` and all `UnityWebRequest` related classes.
