# NetworkManager Callbacks

{% hint style="info" %}
See also [NetworkManager](https://storage.googleapis.com/mirror-api-docs/html/d7/d5a/class\_mirror\_1\_1\_network\_manager.html) in the API Reference.
{% endhint %}

There are a number of events that can occur over the course of the normal operation of a multiplayer game, such as the host starting up, a player joining, or a player leaving. Each of these possible events has an associated callback that you can implement in your own code to take action when the event occurs.

To do this for the `NetworkManager`, you need to create your own script which inherits from `NetworkManager`. You can then override the virtual methods on `NetworkManager` with your own implementation of what should happen when the given event occurs.

This page lists all the virtual methods (the callbacks) that you can implement on the `NetworkManager`, and when they occur. The callbacks that occur, and the order they occur, vary slightly depending on which mode your game is running in, so each mode’s callbacks are listed separately below.

A game can be running in one of three modes, host, client, or server-only. The callbacks for each mode are listed below:

## Host Mode: <a href="#host-mode" id="host-mode"></a>

When the host is started:

* `OnStartServer`
* `OnStartHost`
* `OnServerConnect`
* `OnStartClient`
* `OnClientConnect`
* `OnServerSceneChanged`
* `OnServerReady`
* `OnServerAddPlayer`
* `OnClientChangeScene`
* `OnClientSceneChanged`

When a client connects:

* `OnServerConnect`
* `OnServerReady`
* `OnServerAddPlayer`

When a client disconnects:

* `OnServerDisconnect`

When the host is stopped:

* `OnStopHost`
* `OnServerDisconnect`
* `OnStopClient`
* `OnStopServer`

## Client Mode <a href="#client-mode" id="client-mode"></a>

When the client starts:

* `OnStartClient`
* `OnClientConnect`
* `OnClientChangeScene`
* `OnClientSceneChanged`

When the client stops:

* `OnStopClient`
* `OnClientDisconnect`

## Server Mode <a href="#server-mode" id="server-mode"></a>

When the server starts:

* `OnStartServer`
* `OnServerSceneChanged`

When a client connects:

* `OnServerConnect`
* `OnServerReady`
* `OnServerAddPlayer`

When a client disconnects:

* `OnServerDisconnect`

When the server stops:

* `OnStopServer`
