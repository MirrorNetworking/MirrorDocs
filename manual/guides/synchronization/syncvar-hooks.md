# SyncVar Hooks

The hook attribute can be used to specify a function to be called when the SyncVar changes value on the client.

* The Hook method must have two parameters of the same type as the SyncVar property. One for the old value, one for the new value.
* The Hook is always called after the property value is set. You don't need to set it yourself.
* The Hook only fires for changed values, and changing a value in the inspector will not trigger an update.
* As of version 11.1.4 (March 2020) and later, hooks can be virtual methods and overriden in a derived class.

Below is a simple example of assigning a random color to each player when they're spawned on the server. All clients will see all players in the correct colors, even if they join later.

> Note: The signature for hook methods was changed in version 9.0 (Feb 2020) to having 2 parameters (old and new values). If you're on an older version, hook methods just have one parameter (new value).

```csharp
using UnityEngine;
using Mirror;

public class PlayerController : NetworkBehaviour
{
    [SyncVar(hook = nameof(OnColorChanged))]
    Color playerColor = Color.black;

    // Unity makes a clone of the Material every time GetComponent().material is used.
    // Cache it here and Destroy it in OnDestroy to prevent a memory leak.
    Material cachedMaterial;

    public override void OnStartServer()
    {
        playerColor = Random.ColorHSV(0f, 1f, 1f, 1f, 0.5f, 1f);
    }

    void OnColorChanged(Color oldColor, Color newColor)
    {
        if (cachedMaterial == null)
            cachedMaterial = GetComponent().material;

        cachedMaterial.color = newColor;
    }

    void OnDestroy()
    {
        Destroy(cachedMaterial);
    }
}
```

## Hook call order <a href="#hook-call-order" id="hook-call-order"></a>

Hooks are invoked in the order the syncvars are defined in the file.

```csharp
public class MyBehaviour : NetworkBehaviour 
{
    [SyncVar] 
    int X;

    [SyncVar(hook = nameof(Hook1))] 
    int Y;

    [SyncVar(hook = nameof(Hook2))]
    int Z;
}
```

if X, Y, and Z are all set on the server at the same time then the call order will be:

1. X value is set
2. Y value is set
3. Hook1 is called
4. Z value is set
5. Hook2 is called
