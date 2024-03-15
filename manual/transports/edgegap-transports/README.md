# Edgegap Transports

[Edgegap's Distributed Relay](https://edgegap.com/en/platform/distributed-relay) Service offers relays in their many locations all over the world, allowing for player hosted games without having to worry about NAT or latency issues.

In order to use the relay service, you will need to either [set up your own backend to start relay sessions on-demand](https://docs.edgegap.com/docs/relay-edgegap-api) or you can use Edgegap's [Lobby](https://docs.edgegap.com/docs/lobby/service) or [Matchmaker ](https://docs.edgegap.com/docs/matchmaker)service to do this for you (and as an added bonus you get lobbies or matchmaking on-top).

Mirror offers the following transports to interact with the relay service:

* [Edgegap Relay](edgegap-relay.md): Barebones relay transport using KCP
* [Edgegap Lobby](edgegap-lobby.md): Simple to use relay transport using Edgegap's Lobby Service

