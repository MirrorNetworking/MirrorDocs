# Reverse Proxy

<div align="left">

<figure><img src="../../../../.gitbook/assets/image (56).png" alt=""><figcaption><p>WebGL with Reverse Proxy - 500 CCU</p></figcaption></figure>

</div>

Using a reverse proxy performs better than doing SSL encryption within Unity as part of the game server process:

* Unity is single-threaded, while the reverse proxy is a separate process that's generally engineered to enterprise class performance and is multi-threaded.
* Encryption is computationally expensive, so better to not bog down Unity with that workload.
* Unity / mono may not be up to date on TLS version or be able to read the latest certificates.

This section has instructions for both [Linux](linux/) and [Windows](windows/).
