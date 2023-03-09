# TCP and UDP

TCP and UDP are both protocols used to send information over the internet - in fact, they are the two most commonly used internet protocols in the world: TCP was developed in the 1970s, while UDP was introduced in the 1980s. However, it is important to note two key differences between TCP and UDP communications:

* TCP has built-in reliability, ensuring data gets from A to B correctly with a higher latency trade-off.
* UDP has lower latency with it's data transfer, but the raw protocol is unreliable by nature and makes no assurances data will be sent from A to B correctly.

## [TCP (Transmission Control Protocol)](https://en.wikipedia.org/wiki/Transmission\_Control\_Protocol) <a href="#tcp-transmission-control-protocol" id="tcp-transmission-control-protocol"></a>

As stated before, TCP is the most popular protocols on the internet. TCP is used for HTTP, SSH, FTP, and many more applications. TCP core features make it easy for programers to work with the protocol, since a programmer will be able to assure data will be sent from A to B correctly. However, while this sounds great, it can introduce higher latency when connections are malfunctioning or experiencing severe turbulence.

In a game environment, TCP is better for slower paced games where latency isn't important but the game data is.

### Key features include <a href="#key-features-include" id="key-features-include"></a>

* **Reliable:** Applications don't have to worry about missing packets. If a packet gets lost, TCP will resend it. All data is either transmitted successfully or you get an error and the connection is closed.
* **Sequenced:** TCP guarantees that every message will arrive in the same order it was sent. If you send "a" then "b" you will receive "a" then "b" on the other side as well.
* **Connection oriented:** TCP has the concept of a connection. A connection will stay open until either the client or server decides to close it. Both the client and server get notified when the connection ends.
* **Congestion control:** If a server is being overwhelmed, TCP will throttle the data to avoid congestion collapse.

### Transports using the TCP protocol <a href="#transports" id="transports"></a>

* [Telepathy](../transports/telepathy-transport.md)
* [WebSockets](../transports/websockets-transport/)

## [UDP (User Datagram Protocol)](https://en.wikipedia.org/wiki/User\_Datagram\_Protocol) <a href="#udp-user-datagram-protocol" id="udp-user-datagram-protocol"></a>

UDP is used for real time applications such as fast paced action games or Voice over IP (VoIP), where low latency is more important than reliability. Application examples include Skype, Discord, Zoom and many others.&#x20;

In a game environment, the raw power of UDP can be harnessed to allow for a greater control of how data is being sent, allowing non-critical data to be sent faster. This in turn makes UDP better for fast paced games where latency between server and client is important and if a few packets are lost, the game can recover.

### Key features include <a href="#key-features-include-1" id="key-features-include-1"></a>

* **Low Latency:** UDP is faster because it doesn't need to wait for the remote side to acknowledge packets. Instead, it can send keep sending new data packets one after the other. It is also known as a "scattershot" protocol, since it'll just shoot data at clients with no assurances that they'll actually get the data.
* **Channel support:** Channels allow for different delivery types. Depending on the implementation, one channel can be used for critical data that needs to get to the destination, while a different channel can be used for "send and forget" data transfer without any reliability.
* **Different packet types:** Depending on the implementation on top of the UDP protocol, some transports offer different packet sending methods, such as Reliable Ordered, Reliable Unordered, Unreliable, and more depending on the implementation. Reliable UDP transfer depends on the implementation, but it is usually modelled after TCP's reliability system.

### Transports using the UDP Protocol <a href="#transports-1" id="transports-1"></a>

* [Ignorance](../transports/ignorance.md)
* [KCP](../transports/kcp-transport.md)
* [LiteNetLib](../transports/litenetlib-transport.md)

## The choice is yours <a href="#the-choice-is-yours" id="the-choice-is-yours"></a>

Mirror is transport independent, they can simply by added to your NetworkManager GameObject. Mirror comes with a [KCP](../transports/kcp-transport.md) transport _(all platforms except WebGL)_ and a [Websocket](../transports/websockets-transport/) _(WebGL)_ transport by default. See the [Transports](../transports/) page for more about transports.

Pick whatever transport works best for you and your game. We recommend you profile your game's networking and collect real world numbers before you make a final decision.
