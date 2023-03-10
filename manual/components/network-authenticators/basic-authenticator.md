# Basic Authenticator

Mirror includes a Basic Authenticator in the Mirror / Authenticators folder which just uses a simple username and password.

* Drag the Basic Authenticator script to the inspector of the object in your scene that has Network Manager
* The Basic Authenticator component will automatically be assigned to the Authenticator field in Network Manager

When you're done, it should look like this:

![Network Manager with Basic Authenticator assigned](<../../../.gitbook/assets/image (28).png>)

![Basic Authenticator](<../../../.gitbook/assets/image (105).png>)

* Server Credentials can be set at design time or runtime
* Client Credentials would be set at runtime, e.g. from UI input fields, **before** calling `NetworkManager.singleton.StartClient();`

{% hint style="info" %}
You don't need to assign anything to the event lists unless you want to subscribe to the events in your own code for your own purposes. Mirror has internal listeners for both events.
{% endhint %}
