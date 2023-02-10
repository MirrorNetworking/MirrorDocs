# Network Authenticators

When you have a multiplayer game, often you need to store information about your player for later games, keep game stats or communicate with your friends. For all these use cases, you often need a way to uniquely identify a user. Being able to tell users apart is called authentication. There are several methods available, some examples include:

* Ask the user for username and password
* Use a third party OAuth2 or OpenID identity provider, such as Facebook, Twitter, Google
* Use a third party service such as PlayFab, GameLift or Steam
* Use the device id, very popular method in mobile
* Use Google Play in Android
* Use Game Center in IOS
* Use a web service in your website

## Encryption Notice <a href="#encryption-notice" id="encryption-notice"></a>

Few of the available transports support encryption, so if you want to do authentication through Mirror, we highly recommend you use a transport that does, e.g. [SimpleWebSocket ](https://mirror-networking.gitbook.io/docs/transports/websockets-transport)Transport, or carry out your exchange of sensitive data using UnityWebRequest with a service over HTTPS.  This can be done inside an Authenticator, or before calling `StartClient`.

## Offline / Online Scenes

Authentication takes place in the Offline scene, and messages are exchanged with the game server in a pre-connect phase, so client stays in the Offline scene until authentication completes.

## Persisting Data

`NetworkConnection` has an **`authenticationData`** property of type `Object` that can be set to pretty much anything you need, such as account id's, tokens, character selection, etc., including a struct of data, on the server and/or client during the authentication process.  That data is available everywhere else in Mirror where you have the client's `NetworkConnection`...just cast it back to whatever type you put into it.

## Built-in Authenticators <a href="#basic-authenticator" id="basic-authenticator"></a>

* [Basic Authenticator](basic-authenticator.md)\
  Mirror includes a Basic Authenticator in the Mirror / Authenticators folder which just uses a simple username and password.
*   [Device Authenticator](device-authenticator.md)

    Uses `SystemInfo.deviceUniqueIdentifier` on supported platforms, and GUID in PlayerPrefs as a fallback for platforms that don't.

## Custom Authenticators <a href="#custom-authenticators" id="custom-authenticators"></a>

Authenticators are derived from an `Authenticator` abstract class that allows you to implement any authentication scheme you need.

From the Assets menu, click Create > Mirror > Network Authenticator to make your own custom Authenticator from our [Script Templates](../../general/script-templates.md), and just fill in the messages and validation code to suit your needs. When a client is successfully authenticated, call `ServerAccept(conn)` on the server and `ClientAccept()` on the client. Mirror is listening for these events to proceed with the connection sequence. Subscribe to `OnServerAuthenticated` and `OnClientAuthenticated` events if you wish to perform additional steps after authentication.

## Message Registration <a href="#message-registration" id="message-registration"></a>

By default all messages registered to `NetworkServer` and `NetworkClient` require authentication unless explicitly indicated otherwise. To register messages to bypass authentication, you need to specify `false` for a bool parameter to the `RegisterMessage` method:

```csharp
NetworkServer.RegisterHandler(OnAuthRequestMessage, false);
```

Certain internal messages already have been set to bypass authentication:

* Server
  * `NetworkPingMessage`
* Client
  * `SceneMessage`
  * `NetworkPongMessage`

## Tips

* Register handlers for messages in `OnStartServer` and `OnStartClient`. They're called from StartServer/StartHost, and StartClient, respectively.
* Send a message to the client if authentication fails, especially if there's some issue they can resolve.
* Call the `Disconnect()` method of the `NetworkConnection` on the server and client when authentication fails. If you want to give the user a few tries to get their credentials right, you certainly can, but Mirror will not do the disconnect for you.
  * Remember to put a small delay on the Disconnect call on the server if you send a failure message so that it has a chance to be delivered before the connection is dropped. At a minimum, yield one frame.

Now that you have the foundation of a custom Authenticator component, the rest is up to you. You can exchange any number of custom messages between the server and client as necessary to complete your authentication process before approving the client.

Authentication can also be extended to character selection and customization, just by crafting additional messages and exchanging them with the client before completing the authentication process. This means this process takes place before the client player actually enters the game or changes to the Online scene.

If you write a good authenticator, consider sharing it with other users or donating it to the Mirror project.
