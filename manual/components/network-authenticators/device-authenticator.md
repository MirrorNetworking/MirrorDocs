# Device Authenticator

Device Authenticator uses the `SystemInfo.deviceUniqueIdentifier` to identify the client.

For platforms that don't support `deviceUniqueIdentifier` a GUID is generated and stored in `PlayerPrefs`.

{% hint style="warning" %}
NOTE: deviceUniqueIdentifier can be spoofed, so there's no guarantee of security.
{% endhint %}

* Drag the Device Authenticator script to the inspector of the object in your scene that has Network Manager
* The Device Authenticator component will automatically be assigned to the Authenticator field in Network Manager

When you're done, it should look like this:

<div align="left">

<img src="../../../.gitbook/assets/image (108).png" alt="Network Manager with Device Authenticator assigned">

</div>

<div align="left">

<img src="../../../.gitbook/assets/image (18).png" alt="Device Authenticator">

</div>

{% hint style="info" %}
You don't need to assign anything to the event lists unless you want to subscribe to the events in your own code for your own purposes. Mirror has internal listeners for both events.
{% endhint %}
