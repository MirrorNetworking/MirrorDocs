# Linux

On Linux, you have a wide variety of choices in what software you can use as the reverse proxy. Common options may include [Nginx](nginx.md), [Caddy](caddy.md), [Apache](apache.md), [HAProxy](ha-proxy.md) and more.

We'll go into detail for a few of the common choices here, generally you likely just want to stick with whatever software you're familiar with or already using.

The following guides (aside from caddy) will assume you have already set up certbot and gotten certs issued. They're also written for Debian but should be very similar (if not identical) on other distros.

Make sure Simple Web Transport is set up like this:

* Ssl Enabled: **not checked**
* Ssl Protocols: **Tls 12**
* Server Port: **27777**
* Client Use Wss: **checked**
* Client Port Option: **Specify Port**
* Custom Client Port: **7777**

<div align="left">

<figure><img src="../../../../../.gitbook/assets/image (153).png" alt=""><figcaption><p>Simple Web Transport with Reverse Proxy Settings</p></figcaption></figure>

</div>
