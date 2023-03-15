# Latency Simulaton Transport

Mirror's Latency Simulation transport allows you to test your project under non-ideal network conditions.

Add it to NetworkManager, **wrap** it around your regular Transport, drag it into NetworkManager.transport.

It can simulate:

* Latency in milliseconds
* Packet loss in %
* Packet scramble / reorder

{% hint style="info" %}
Reliable messages are ordered and guaranteed delivery by definition. Packet loss / scramble over reliable manifests itself via latency.&#x20;
{% endhint %}

![](../../.gitbook/assets/2021-03-14\_21-32-23@2x.png)

![NMLatencySim](https://user-images.githubusercontent.com/57072365/225437999-0667da1b-abf7-49c5-8c1d-d1a2fec36b12.jpg)
