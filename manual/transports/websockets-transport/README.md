# WebSockets Transport

A Mirror transport that uses the [websocket protocol](https://en.wikipedia.org/wiki/WebSocket), thus enabling multiplayer in the browser from unity webgl builds!

![Simple Web Transport Inspector](<../../../.gitbook/assets/simple-web-transport-current-inspector-view.png>)

## SSL Settings
SSL settings is obsolete and may be removed in a future release.
{% hint style="info" %}
NOTE: WebGL performs a lot better with a Reverse Proxy, and that's generally easier to set up and maintain than using cert.json and PFX files.

\
Go to the [Reverse Proxy](../reverse-proxy/) page for instructions.
{% endhint %}

For legacy purposes, you can read more about simple web transport and ssl in this [SSL guide](ssl/).

## Server Settings
Specify what port the websocket based game server listens on.

## Client Settings
These settings allow for users to customize web game client behavior.

For example, you may want to introduce a gateway server (aka reverse proxy server) and you want to configure the web game clients to connect to the gateway server instead of the game server directly. These settings enable you to do that.

## Logging <a href="#logging" id="logging"></a>

Log levels can be set using the dropdown on the transport or setting `Mirror.SimpleWeb.Log.level`.

The transport applies the dropdown value in its `Awake` and `OnValidate` methods.

#### Log methods <a href="#log-methods" id="log-methods"></a>

Log methods in this transport use [ConditionalAttribute](https://docs.microsoft.com/en-us/dotnet/api/system.diagnostics.conditionalattribute?view=netstandard-2.0) so they are removed depending on the preprocessor defines.

These preprocessor defines effect the logging:

* `DEBUG` allows warn/error logs
* `SIMPLEWEB_LOG_ENABLED` allows all logs

{% hint style="info" %}
NOTE: without `SIMPLEWEB_LOG_ENABLED` set to "info" or "verbose" logging will never happen (even if log level allows it).
{% endhint %}

See the [Unity docs](https://docs.unity3d.com/Manual/PlatformDependentCompilation.html) on how to set custom defines.
