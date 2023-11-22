# Multiplex Transport

The Multiplex Transport is not a transport itself, but it allows you to combine other transports so that your clients can connect to your servers via either one of them.

A common use case for the Multiplex Transport is a server listening for both WebGL and mobile  / desktop clients. Your WebGL clients can connect to the server using a [Websocket Transport](websockets-transport/) and your mobile or desktop clients can connect to the same server via TCP or UDP transports. You can configure any number of transports in the Multiplex Transport.

To use the Multiplex Transport follow these steps:

1. Add a game object with a Network Manager to your scene if you have not done so
2. By default, Mirror will add KCP Transport to your Network Manager game object
3. Add a Multiplex Transport component to the game object
4. Assign the Multiplex Transport component in your Network Manager's transport field
5. Add a Websocket Transport component to the game object
6. Add the KCP Transport component to the Multiplex Transport as the first transport
7. Add the Websocket Transport component to the Multiplex Transport as the second transport

Please note that KCP and Websocket Transport cannot listen to the same port. By default KCP listens to 7777 (UDP) and the websocket transport listens to 7778 (TCP).

If you build your game as a WebGL game, the KCP Transport will be skipped and your client will use the websocket transport. If you build your game as a mobile or desktop app, it will choose KCP Transport. The server will happily accept connections from both.

Here is what the configuration should look like:

<div align="left">

<img src="../../.gitbook/assets/image (103).png" alt="">

</div>
