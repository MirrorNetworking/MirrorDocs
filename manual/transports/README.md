# Transports

## Built-in Transports

These transports are included with Mirror.

* [KCP ](kcp-transport.md)UDP transport based on kcp.c, line-by-line translation to C#
* [Telepathy](telepathy-transport.md) - Simple, message based, MMO Scale TCP networking in C#. And no magic.
* [Simple Web Sockets](websockets-transport/) - WebGL transport layer for Mirror that target browser clients.
* [Multiplexer](multiplex-transport.md) - Bridging transport to allow a server to handle clients on different transports concurrently, for example desktop clients using Telepathy together with WebGL clients using Websockets.
* [Latency Simulation](latency-simulaton-transport.md) - Middleman transport to test non-ideal network conditions
* [Encryption](encryption-transport.md) - Middleman transport to encrypt a different transport
* [Edgegap Relay Transports](edgegap-transports/) - Transports to utilize [Edgegaps Destributed Relay](https://edgegap.com/en/platform/distributed-relay) Service

## Additional Transports

These transports are maintained by third parties outside of Mirror.

* [Monke](https://github.com/JesusLuvsYooh/monke) - plug and play encrypted middleman transport layer for mirror.
* [Ignorance](ignorance.md) - reliable and unreliable sequenced UDP transport based on ENet.
* [LiteNetLibTransport](litenetlib-transport.md) - UDP transport based on [LiteNetLib](https://github.com/RevenantX/LiteNetLib).

## Relay Transports

These transports are maintained by third parties and use relay infrastructure to connect clients to servers behind firewalls / NAT.

* [Steam - FizzySteamworks](fizzysteamworks-transport.md) - Transport utilizing Steam P2P network, building on [Steamworks.NET](https://github.com/rlabrecque/Steamworks.NET).
* [Epic - Epic Online Services](https://github.com/Ludogram/EpicOnlineTransport) - Relay transport utilizing Epic's free relay service.
* [LRM - Light Reflective Mirror](https://github.com/Speidy674/Light-Reflective-Mirror) - Relay transport for WebGL clients.

## Changing Transports

Changing transports is very easy and only requires a few steps:

* Go to the game object that has the Network Manager component
* Add another transport script via the Add Component button
* Drag the transport script to the "Transport" field in the Network Manager
* Remove the old transport script (optional)

If you have connection issues with a transport that requires port forwarding, make sure to port forward the correct protocol (TCP / UDP).
