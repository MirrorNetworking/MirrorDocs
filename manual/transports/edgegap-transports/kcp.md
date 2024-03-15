# Edgegap Relay

The `EdgegapKcpTransport` is a bare-bones transport using [KCP](../kcp-transport.md) through Edgegap's distributed relay.&#x20;

It expects you to set up the relay session and communicate the details with all players yourself.

Once you have done so, set the `relayAdddress`, `sessionId`, `userId` and the respective `relayGameClientPort` or `relayGameServerPort` depending on if the player is hosting or not.

All that is left, once the details are filled in, is to start the Client/Server/Host as usual via the NetworkManager instance.
