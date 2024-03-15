# Edgegap Lobby

The `EdgegapLobbyKcpTransport` uses Edgegap's [Lobby Service](https://docs.edgegap.com/docs/lobby/functions) to start relay sessions and manage lobbies for you. It uses [KCP](../kcp-transport.md) for the actual connection.

It is by far the easiest way to use relays and requires basically no setup, if all you need is peer-to-peer/player hosted lobbies with no matchmaking.

The lobby transport allows you to create/join lobbies and list available lobbies for players to select.

We provide a [simple lobby UI example to let you hit the ground running](../../examples/edgegap-lobby.md)

### Setup

Setup couldn't be simpler, all you need to do is deploy a lobby service. You can either do this [manually via the Edgegap API](https://docs.edgegap.com/api/#tag/Lobbies/operation/lobby-create) or use the built-in UI on the transport.

<figure><img src="../../../.gitbook/assets/image (151).png" alt=""><figcaption><p>Create &#x26; Deploy Lobby button on the EdgegapLobbyKcpTransport</p></figcaption></figure>

Once you have clicked the "Create\&Deploy Lobby" button, a window will open:

<figure><img src="../../../.gitbook/assets/image (154).png" alt=""><figcaption><p>Create &#x26; Deploy Lobby window</p></figcaption></figure>

Simply enter your edgegap API key (if you don't have one, clicking the "I have no api key?" button will conveniently direct you to the edgegap dashboard page where you can create one) and a unique lobby name of your choosing and hit create. It will take a brief moment, as it waits until the service is fully deployed, and then the lobby url on the transport will be filled out for you.

Once you've done that, you're all ready to go.
