# FAQ

## How to Send/Sync custom data types?

Mirror can automatically create Serialization functions for many custom data types when your scripts are compiled.

For example, mirror will automatically create a functions for `MyCustomStruct` so that it can be sent without any extra work.

```csharp
[ClientRpc]
public void RpcDoSomething(MyCustomStruct data)
{
    // do stuff here
}

struct MyCustomStruct
{
    int someNumber;
    Vector3 somePosition;
}
```

For More details

* [Data Types](https://mirror-networking.gitbook.io/docs/guides/data-types)
* [Serialization](https://mirror-networking.gitbook.io/docs/guides/serialization)

## How do I connect to another Mirror instance?

### To another instance on the same device

Ensure you're connecting to "localhost". Localhost basically means "local host" which is your local machine.

Make sure the Network Address field on NetworkManager is set to "localhost", or if using the NetworkManagerHUD that the text box is "localhost". Make sure you don't include the quotes.

Please note that this won't work on mobile devices, as neither iOS or Android support running two instances of the same application side-by-side. This is more suited for desktop testing and development use.

### **To another instance on the same network (LAN)**

Make sure the Network Address field on NetworkManager is set to the LAN IP of the host, or if using the NetworkManagerHUD that the text box is set to the LAN IP of the host.

A correct setting, for example, is "192.168.8.100", "10.0.0.100", "172.16.42.69"\
An incorrect setting, for example, is "localhost" or "203.200.110.100"&#x20;

_In some cases you may need additional steps, check below_

To check your LAN IP on Windows you can open PowerShell/Command Prompt and use the `ipconfig` command, then under your current adapter (ethernet/wifi/etc) look for the _IPv4 Address_ entry like so:

`IPv4 Address. . . . . . . . . . . : 192.168.x.x`

On Mac OS, you can use Network Settings in the Settings application, while on Linux you can either use `ifconfig`, `ip addr` or a GUI like _NetworkManager_ or _wicd_ to get your LAN IP depending if you are running a desktop environment or going commando at the command line.

### To another instance (Internet/WAN)

Set the networkAddress field to be the IP address of the host (google 'whats my IP')

{% hint style="warning" %}
This section does not cover Relays, Dedicated VPSes or Headless Features.
{% endhint %}

For this to work, you will need to do **some** of the following, most of these depend on your set up and router:

* **Port forwarding**:
  * Ensure your ISP allows you to host servers on your own connection, some ISPs do not allow this and will filter incoming connections. **If in doubt, check with your ISP.** _Knowledge is power._
  * Ensure your ISP allows you to change settings on your router. Some can be very strict and reset settings that you changed randomly.
  * Either forward your game port (default is 7777) for your PC's local IP, 192.168.1.20 for example. Ensure that you specify the correct protocol, incoming UDP connections won't work if you have it only set to accept incoming TCP connections and vice versa. If your router supports it, you can use the "BOTH" setting to bypass this headache; **OR**
  * Pull a quick (but less safe) trick add that local IP to the routers' DMZ for desperate measures.

{% hint style="danger" %}
DMZ can be a very useful tool but **be warned:** _with great power comes great responsibility!_ \
The computer that has the IP address you specify will be exposed on the internet without the router firewall filtering bad inbound traffic. That means if you're running any private web server or application, you may have intrusions from others located elsewhere on the internet.\
\
**DO NOT USE** this DMZ option if you are running an unpatched operating system (for example, a old version of Microsoft Windows) or you risk your computer security being compromised. Ensure your security is enabled and your anti-virus/anti-malware software is fully updated before playing with DMZ settings.
{% endhint %}

* **PC Firewalls**:
  * You can turn it off for a quick test to see if your firewall is being annoying. **Make sure you turn it back on later.**
  * Manually allow the editor and any builds you create in your Firewall settings.
* Try from a build rather than the Unity Editor as sometimes Unity Editor can be janky.
* Some anti-virus/mobile devices may have additional blocking layers:
  * If you can, try turning them off for a quick test. **Ensure you turn it back on later.**
* In rare cases ISPs or companies/schools block ports and connections, this is harder to adjust yourself.

If you need more help it is best to google for guide for your setup and router. An sure-fire alternative to the above is to use a Dedicated Server/VPS or use a Relay.

## Host Migration

Host migration alternatives and work-around.

Host migration as of writing is not built into Mirror, and it is best to avoid Host Migration completely if you can. Below are some tips as to why, and how you can add a host migration-like alternative.

* Dedicated hosts should rarely ever be closed, if you are doing games that need to stay open such as MMO's.
* Short arena maps, take players back to the games list/matchmaker, so they can just join another, nice and simple.

The work around is to basically fake the host migration, store info of a backup host on players game, upon disconnection, reconnect everyone in the game to that new host, then restore positions and variable data back to how it was before the original host disappeared.

* Test players connections when they join, find one with unblocked ports, and decent ping/latency.
* Send this players data (IP and Port) on all connected players games.
* Save various player info, either locally or on that backup host, such as player positions, health etc
* Upon disconnection from server, call a function to connect to the backup hoster. For example, `StartClient( BackupIP - BackupPort )`
* As the scenes will most likely reset, along with players re-spawning, you now need to set player position back to your stored one that was saved either via checkpoints, or in the disconnect detection callback.
* Cover all this up with a UI, saying please wait (optional, should happen in the blink of an eye).

Depending on what your game is like, it'll either be easy or difficult to add the work-around. An example of these are:

* (easier) A game only needing player position data such as "Fall Guys".
* (difficult) Forge of Empires, a game where created objects are placed, soldiers, vehicles, various other crafts and upgrades, all with their own levels/stats.

## Master / List Servers and Simple Matchmaker

A database of world-wide registered host data.

All the hosts, dedicated or player hosts, get added into a list database, players get the list and can choose who to join. Using a list server means players do not have to manually enter IP addresses and Ports, it is all done behind the scenes, and works for localhost, LAN, and WAN connection types. You can show as much, or as little data as you like to the players, such as host name, type (deathmatch), player count (45 / 50), ping, enemy difficulty, map, region etc

* Node ListServer: Free, but you host the files yourself on an unblocked PC, like a VPS. Has a wide variety of customisable features, best option if you want self hosted dedicated games.
* Light Reflective Mirror: This is a list server and a relay, it is free, but you host and manage the files yourself. Relays offer an unblocked route of traffic, but at the cost of extra latency/increased ping. This relay will test for a direct connection first, then fallback to routed traffic if that fails. Both the list server and relayed traffic is optional, you can use one part, and not the other. This is the best option for player hosted games, where router port blocks are common.
* Epic and Steam, these are hosted for you.\
  Check Discords Addons and Transports sections for more information, and for other non-listed ways of matchmaking and list servers.&#x20;

### Simple Matchmaker

* You can make matchmakers out of these list servers, simply hide the list to players, have them auto join a game with space. You could also filter out various requirements the player has set, for example, "USA" Region only, or "Lava Island" map.

## How to get the player count?

There are a few ways to do this, each with their own unique benefit.

**NetworkServer.connections.Count**

* Socket connections, includes people without spawned prefabs, non authenticated, or that may have bugged out during spawn, but have connected temporarily (Android users minimising game). Only host / server can check this.

**NetworkManager.singleton.numPlayers**

* Number of active spawned player objects on the server (only the host / server can check this)

**Find by GameObject Tag**

* Works client-side, doesn't require network code, and a good way to distinguish between player states, by applying gameobject tags for certain situations, for example, 'not ready/Default', 'Dead', 'Spectator'.

```csharp
public GameObject[] playersArray;

public void FindPlayersByTag()
{
  playersArray = GameObject.FindGameObjectsWithTag("Player");
   Debug.Log("Players found: " + playersArray.Length);
}
```
