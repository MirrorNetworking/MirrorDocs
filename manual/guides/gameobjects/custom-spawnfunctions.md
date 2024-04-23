# Custom Spawn Functions

You can use spawn handler functions to customize the default behavior when creating spawned game objects on the client. Spawn handler functions ensure you have full control of how you spawn the game object, as well as how you destroy it.

Use `NetworkClient.RegisterSpawnHandler` or `NetworkClient.RegisterPrefab` to register functions to spawn and destroy client game objects. The server creates game objects directly, and then spawns them on the clients through this functionality. This functions takes either the asset ID or a prefab and two function delegates: one to handle creating game objects on the client, and one to handle destroying game objects on the client. The asset ID can be a dynamic one, or just the asset ID found on the prefab game object you want to spawn.

The Spawn / Unspawn delegates will look something like this:

**Spawn Handler**

```csharp
GameObject SpawnDelegate(Vector3 position, uint assetId) 
{
    // do stuff here
}
```

or

```csharp
GameObject SpawnDelegate(SpawnMessage msg) 
{
    // do stuff here
}
```

**UnSpawn Handler**

```csharp
void UnSpawnDelegate(GameObject spawned) 
{
    // do stuff here
}
```

When a prefab is saved its `assetId` field will be automatically set. If you want to create prefabs at runtime you will have to generate a new GUID.

### **Generate Prefab At Runtime**

```csharp
// generate a new unique assetId 
System.Guid creatureAssetGuid = System.Guid.NewGuid();
uint creatureAssetId = NetworkIdentity.AssetGuidToUint(creatureAssetGuid);

// register handlers for the new assetId
NetworkClient.RegisterSpawnHandler(creatureAssetId, SpawnCreature, UnSpawnCreature);
```

**Use existing prefab**

```csharp
// register prefab you'd like to custom spawn and pass in handlers
NetworkClient.RegisterPrefab(coinAssetId, SpawnCoin, UnSpawnCoin);
```

**Spawn on Server**

```csharp
// spawn a coin - SpawnCoin is called on client
NetworkServer.Spawn(gameObject, coinAssetId);
```

The spawn functions themselves are implemented with the delegate signature. Here is the coin spawner. The `SpawnCreature` would look the same, but have different spawn logic:

```csharp
public GameObject SpawnCoin(SpawnMessage msg)
{
    return Instantiate(m_CoinPrefab, msg.position, msg.rotation);
}

public void UnSpawnCoin(GameObject spawned)
{
    Destroy(spawned);
}
```

When using custom spawn functions, it is sometimes useful to be able to unspawn game objects without destroying them. This can be done by calling `NetworkServer.UnSpawn`. This causes the object to be `Reset` on the server and sends a `ObjectDestroyMessage` to clients. The `ObjectDestroyMessage` will cause the custom unspawn function to be called on the clients. If there is no unspawn function the object will instead be `Destroy`

Note that on the host, game objects are not spawned for the local client, because they already exist on the server. This also means that no spawn or unspawn handler functions are called.

### Pooling Game Objects

To avoid Instantiating & Destroying heavily used GameObjects, it can be useful to pool them instead.

![](../../../.gitbook/assets/2022-04-04\_20-21-49@2x.png)

Here is an example of how you might set up a simple game object pooling system with custom spawn handlers. Spawning and unspawning then puts game objects in or out of the pool.

```csharp
using UnityEngine;

namespace Mirror.Examples
{
    public class PrefabPool : MonoBehaviour
    {
        // singleton for easier access from other scripts
        public static PrefabPool singleton;

        [Header("Settings")]
        public GameObject prefab;

        [Header("Debug")]
        public int currentCount;
        public Pool<GameObject> pool;

        void Start()
        {
            InitializePool();
            singleton = this;
            NetworkClient.RegisterPrefab(prefab, SpawnHandler, UnspawnHandler);
        }

        // used by NetworkClient.RegisterPrefab
        GameObject SpawnHandler(SpawnMessage msg) => Get(msg.position, msg.rotation);

        // used by NetworkClient.RegisterPrefab
        void UnspawnHandler(GameObject spawned) => Return(spawned);

        void OnDestroy()
        {
            NetworkClient.UnregisterPrefab(prefab);
        }

        void InitializePool()
        {
            // create pool with generator function
            pool = new Pool<GameObject>(CreateNew, 5);
        }

        GameObject CreateNew()
        {
            // use this object as parent so that objects dont crowd hierarchy
            GameObject next = Instantiate(prefab, transform);
            next.name = $"{prefab.name}_pooled_{currentCount}";
            next.SetActive(false);
            currentCount++;
            return next;
        }

        // Used to take Object from Pool.
        // Should be used on server to get the next Object
        // Used on client by NetworkClient to spawn objects
        public GameObject Get(Vector3 position, Quaternion rotation)
        {
            GameObject next = pool.Get();

            // set position/rotation and set active
            next.transform.position = position;
            next.transform.rotation = rotation;
            next.SetActive(true);
            return next;
        }

        // Used to put object back into pool so they can b
        // Should be used on server after unspawning an object
        // Used on client by NetworkClient to unspawn objects
        public void Return(GameObject spawned)
        {
            // disable object
            spawned.SetActive(false);

            // add back to pool
            pool.Return(spawned);
        }
    }
}

```

To use this pool, add the `PrefabPool` component (code above) to the NetworkManager. Next, drag a prefab you want to spawn multiple times to the Prefab field.

{% hint style="warning" %}
Make sure to remove the prefab from NetworkManager's spawnable prefabs list. There should only be one way to spawn it. Otherwise Mirror show a warning.
{% endhint %}

We can modify our Tanks example to showcase the Pooling system.

Open Tank.cs and find the CmdFire function:

```csharp
[Command]
void CmdFire()
{
    GameObject projectile = Instantiate(projectilePrefab, projectileMount.position, projectileMount.rotation);
    NetworkServer.Spawn(projectile);
    RpcOnFire();
}
```

Instead of Instantiating, grab a Prefab from the Pool:

```csharp
[Command]
void CmdFire()
{
    GameObject projectile = PrefabPool.singleton.Get(projectileMount.position, projectileMount.rotation);
    NetworkServer.Spawn(projectile);
    RpcOnFire();
}
```

Projectile.cs currently destroys itself via GameObject.Destroy:

```csharp
[Server]
void DestroySelf()
{
    NetworkServer.Destroy(gameObject);
}
```

Instead, we simply Unspawn it and return it to the pool:

```csharp
[Server]
void DestroySelf()
{
    // return to prefab pool
    NetworkServer.UnSpawn(gameObject);
    PrefabPool.singleton.Return(gameObject);
}
```

Press Play and fire some projectiles. Notice how nothing is instantiated. Instead, NetworkManager has a pool of children which are disabled until they are needed.

![](../../../.gitbook/assets/2022-04-04\_20-22-58@2x.png)
