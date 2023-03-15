# Reverse Proxy

<figure><img src="../../../.gitbook/assets/image (38).png" alt=""><figcaption><p>WebGL with Reverse Proxy - 500 CCU</p></figcaption></figure>

Using a reverse proxy performs better than doing SSL encryption within Unity as part of the game server process:

* Unity is single-threaded, while the reverse proxy is a separate process that's generally engineered to enterprise class performance and is multi-threaded.
* Encryption is computationally expensive, so better to not bog down Unity with that workload.
* Unity / mono may not be up to date on TLS version or be able to read the latest certificates.

This page has instructions for both [Linux](reverse-proxy.md#linux) and [Windows](reverse-proxy.md#windows-iis).

## Linux

On linux, you have a wide variety of choices in what software you can use as the reverse proxy. Common options may include [Nginx](https://nginx.org/en/), [Caddy](https://caddyserver.com), [Apache](https://httpd.apache.org/), [HAProxy](https://www.haproxy.org/) and more.

We'll go into detail for a few of the common choices here, generally you likely just want to stick with whatever software you're familiar with or already using.

The following guides (aside from caddy) will assume you have already set up certbot and gotten certs issued. They're also written for debian, but should be very similar (if not identical) on other distros.

### Nginx

Nginx is a fast and lightweight web server that can also function as a reverse proxy. It is commonly used for high traffic websites and applications.

Adding a reverse proxy to nginx is as simple as defining another server in your config. On debian/ubuntu this is usually done by creating a file under `/etc/nginx/sites-enabled/`, for this example we'll use `game.conf`

The following config defines a reverse proxy listening on port 7777 to proxy to 127.0.0.1:27777. You will also want to change the `example.com` domain to the actual domain you're using, as well as the path to the ssl certificates.

`/etc/nginx/sites-enabled/game.conf`

```
# helper map to support connection upgrading, only define this once
map $http_upgrade $connection_upgrade {
    default upgrade;
    ''      close;
}
# the actual reverse proxy server block
server {
    # the server will listen on port 7777 for both ipv4 and ipv6 
    listen 7777 ssl http2;
    listen [::]:7777 ssl http2;
    # here we set the domain
    server_name example.org;

    ssl_certificate /etc/letsencrypt/live/example.org/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.org/privkey.pem;

    location / {
        proxy_pass 'http://127.0.0.1:8777';
        
        proxy_redirect off;
        # very long timeouts to make sure long-running connections aren't interrupted
        # you might be able to reduce these based on your use-case
        proxy_read_timeout 7d;
        proxy_send_timeout 7d;
        
        # The following headers are general settings, not directly used by SWT
        # Tell upstream the host
        proxy_set_header Host $host;
        # Tell upstream real ip & forwarded for header
        proxy_set_header X-Real-IP  $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        # tell upstream this was served via https
        proxy_set_header X-Forwarded-Proto https;
        
        proxy_buffer_size 2k;
        proxy_buffering off;
        
        # websocket support
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;

        # 10s connect timeout
        proxy_connect_timeout 10;
    }
}
```

If you want to define many reverse proxies, you can, instead of repeating the proxy definitions for every server block, extract it into an separate file and include it, like so:

`/etc/nginx/sites-enabled/game.conf`:

```
map $http_upgrade $connection_upgrade {
    default upgrade;
    ''      close;
}
# server 7777 -> 27777
server {
    listen 7777 ssl http2;
    listen [::]:7777 ssl http2;
    set $gs_port 27777;
    include /etc/nginx/reverse_proxy.conf;
}

# server 7778 -> 27778
server {
    listen 7778 ssl http2;
    listen [::]:7778 ssl http2;
    set $gs_port 27778;
    include /etc/nginx/reverse_proxy.conf;
}

# server 7779 -> 27779
server {
    listen 7779 ssl http2;
    listen [::]:7779 ssl http2;
    set $gs_port 27779;
    include /etc/nginx/reverse_proxy.conf;
}
```

`/etc/nginx/reverse_proxy.conf`:

```
# here we set the domain
server_name example.org;

ssl_certificate /etc/letsencrypt/live/example.org/fullchain.pem;
ssl_certificate_key /etc/letsencrypt/live/example.org/privkey.pem;

location / {
    proxy_pass 'http://127.0.0.1:${gs_port}';
    
    proxy_redirect off;
    # very long timeouts to make sure long-running connections aren't interrupted
    # you might be able to reduce these based on your use-case
    proxy_read_timeout 7d;
    proxy_send_timeout 7d;
    
    # The following headers are general settings, not directly used by SWT
    # Tell upstream the host
    proxy_set_header Host $host;
    # Tell upstream real ip & forwarded for header
    proxy_set_header X-Real-IP  $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    # tell upstream this was served via https
    proxy_set_header X-Forwarded-Proto https;
    
    proxy_buffer_size 2k;
    proxy_buffering off;
    
    # websocket support
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection $connection_upgrade;

    # 10s connect timeout
    proxy_connect_timeout 10;
}
```

### Caddy

[Following the official guide](https://caddyserver.com/docs/quick-starts/reverse-proxy), it's as simple as defining a `Caddyfile`:

```
example.com:7777

reverse_proxy:27777
```

Or running caddy via:

```
caddy reverse-proxy --from example.com:7777 --to :27777
```

## Windows / IIS

Once you have your Windows Web Server up and running and RDP access to it, you'll probably need to install a couple optional modules:

* URL Rewrite Module - Download [here](https://iis-umbraco-dr.azurewebsites.net/downloads/microsoft/url-rewrite) and install it.
* Application Request Routing (ARR) - download [here](https://www.microsoft.com/en-us/download/details.aspx?id=47333) and install it.

From the Start menu, type IIS and you'll be able to select Internet Information Services (IIS) Manager.

In IIS Manager, select the server just below Start Page in the left panel, and open Configuration Editor in the Management section near the bottom of the main window.

<figure><img src="../../../.gitbook/assets/image (45) (2).png" alt=""><figcaption><p>IIS Manager - Configuration Editor in Management section</p></figcaption></figure>

Change the Section selector to `system.webServer/proxy` as shown in this image and set Enabled to True and click Apply (top right).

<figure><img src="../../../.gitbook/assets/image (45).png" alt=""><figcaption></figcaption></figure>

### Initial Site Bindings

Now in the left panel of IIS Manager, expand the server and Sites and select the Default Web Site.

* In the right panel, click Bindings.
* In the Bindings window, double-click the single entry that will be there for port 80.
* In the Host name field, type the Fully Qualified Domain Name (FQDN) that you want clients to connect to, and click OK, and then Close the Site Bindings window.
* This will be your domain that you've registered and set up DNS pointing to this web server.

<figure><img src="../../../.gitbook/assets/image (6).png" alt=""><figcaption><p>Edit Site Bindings - Type your FQDN and click OK</p></figcaption></figure>

This is what it should look like when you're done:

<figure><img src="../../../.gitbook/assets/image (2).png" alt=""><figcaption><p>Site Bindings with Host Name</p></figcaption></figure>

### App Pool Recycling

Since all clients will be connected through IIS, which uses a "worker process" that will self-recycle by default at a time interval, we need to disable recycling because that would disconnect all clients.

* With the Default Web Site still selected, click Basic Settings in the right panel, and make note of the name of the Application Pool name shown in that window, then close that window.
* In the left panel, click Application Pools and in the main panel select the one that was assigned in Basic Settings above, and click Recycling in the right panel.
* In the Edit Application Pool Recycling Settings, make sure nothing is checked (as shown below), then click Next, and then Finish.

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption><p>Edit Application Pool Recycling Settings</p></figcaption></figure>

### SSL Certificate

{% hint style="warning" %}
NOTE: DNS records pointing to this server need to be done before this step, and propagated to the world DNS servers.  If you can't reach this site with a browser, setting up SSL using WinAcme won't work.
{% endhint %}

You'll need an SSL certificate for your domain.

* Download WinAcme and save it to its own folder from [here](https://www.win-acme.com/) and unzip it to that same folder (makes updating it convenient later as you can safely overwrite with newer versions).
* Run WACS.exe and follow the prompts to create a Certificate using default settings for the Default site in IIS. You'll update the bindings after this step for the game client and proxy.
* WinAcme creates a scheduled task to update the SSL periodically before it expires.

<figure><img src="../../../.gitbook/assets/image (37).png" alt=""><figcaption><p>WinAcme Console Application</p></figcaption></figure>

### Additional Site Bindings

After successfully installing the SSL with WinAcme, return to IIS Manager, select the Default site, and click Bindings again as you did before.

You'll see that WinAcme has added a binding for port 443 with the same host name you used for port 80.

You can double click the port 443 binding to see how it is set up with the SSL certificate selected.

Click Add to create one more binding for port 7777 as shown below, using the same SSL certificate as was used for port 443.  This will be for the client to connect to the Reverse Proxy that will be set up in later in this document.

<figure><img src="../../../.gitbook/assets/image (37) (2).png" alt=""><figcaption><p>IIS Bindings</p></figcaption></figure>

### WebGL Client

Deploy your WebGL build to the site folder. Make sure Simple Web Transport is set up like this:

* Port `7777`
* Client Use Wss checked
* Ssl Enabled **not** checked
* Ssl Protocol set to Tls 13.

<figure><img src="../../../.gitbook/assets/image (39).png" alt=""><figcaption><p>Simple Web Transport Client Settings</p></figcaption></figure>

### WebGL Server

Deploy your server build to a folder under `c:\` and start it on port `27777`. Make sure you set that correctly in Simple Web Transport when building the server or add code to your Network Manager to force that when running headless mode.

### web.config for IIS

* Right-click the Default site and choose Explore. This is the folder where you'll deploy your WebGL build files.
* If there's already a `web.config` file in that folder, open it in Notepad, otherwise create an empty one and open it.
* Merge the XML below into whatever might already be there, save and close it.
* Go back to IIS, select the server itself, and click Restart in the right panel.

The web.config file below assumes several things:

* You've built your server to listen on port `27777` and the WebGL client to connect to `7777`.
* You've built your WebGL client using brotli (preferred) or gzip compression.
* You've set up the bindings as shown above.

```
<configuration>
    <system.webServer>
        <!--
        Compressed Unity builds without decompression fallback can't be properly hosted on a server which
        has static compression enabled because this might result in the build files being compressed twice.
        The following line disables static server compression.
        -->
        <urlCompression doStaticCompression="false" doDynamicCompression="false" />
 
        <!-- To host compressed Unity builds, the correct mimeType should be set for the compressed build files. -->
        <staticContent>
            <!--
            NOTE: IIS will throw an exception if a mimeType is specified multiple times for the same extension.
            To avoid possible conflicts with configurations that are already on the server, we remove the mimeType for
            the corresponding extension using the <remove> element, before adding mimeType using the <mimeMap> element.
 
            The following lines are required for builds compressed with gzip, which don't include decompression fallback.
            The correct MIME type here would be application/octet-stream, but due to Safari bug
            https://bugs.webkit.org/show_bug.cgi?id=247421, it's preferable to use MIME Type application/gzip instead.
            -->
            <remove fileExtension=".data.gz" />
            <mimeMap fileExtension=".data.gz" mimeType="application/gzip" />
            <remove fileExtension=".wasm.gz" />
            <mimeMap fileExtension=".wasm.gz" mimeType="application/wasm" />
            <remove fileExtension=".js.gz" />
            <mimeMap fileExtension=".js.gz" mimeType="application/javascript" />
            <remove fileExtension=".symbols.json.gz" />
            <mimeMap fileExtension=".symbols.json.gz" mimeType="application/octet-stream" />
 
            <!--
            The following lines are required for builds compressed with Brotli, which don't include decompression fallback.
            -->
            <remove fileExtension=".data.br" />
            <mimeMap fileExtension=".data.br" mimeType="application/octet-stream" />
            <remove fileExtension=".wasm.br" />
            <mimeMap fileExtension=".wasm.br" mimeType="application/wasm" />
            <remove fileExtension=".js.br" />
            <mimeMap fileExtension=".js.br" mimeType="application/javascript" />
            <remove fileExtension=".symbols.json.br" />
            <mimeMap fileExtension=".symbols.json.br" mimeType="application/octet-stream" />
        </staticContent>
 
        <!--
        NOTE: IIS will throw an exception if the following section is used without the "URL Rewrite" module installed.
        Download the "URL Rewrite" module from https://www.iis.net/downloads/microsoft/url-rewrite
        -->
        <rewrite>
            <rules>
                <clear />
                <rule name="Force HTTPS" enabled="true" stopProcessing="true">
                    <match url="(.*)" />
                    <conditions logicalGrouping="MatchAll" trackAllCaptures="false">
                        <add input="{HTTPS}" pattern="^OFF$" />
                    </conditions>
                    <action type="Redirect" url="https://{HTTP_HOST}{REQUEST_URI}" redirectType="Permanent" />
                </rule>
                <rule name="Allow 443" enabled="true" stopProcessing="true">
                    <match url="(.*)" />
                    <conditions logicalGrouping="MatchAll" trackAllCaptures="false">
                        <add input="{SERVER_PORT}" pattern="443" />
                    </conditions>
                    <action type="None" />
                </rule>
                <rule name="Reverse Proxy" enabled="true" stopProcessing="true">
                    <match url=".*" />
                    <conditions logicalGrouping="MatchAll" trackAllCaptures="false">
                        <add input="{SERVER_PORT}" pattern="7777" />
                    </conditions>
                    <action type="Rewrite" url="http://localhost:27777" appendQueryString="false" logRewrittenUrl="true" />
                </rule>
            </rules>
        </rewrite>
 
        <outboundRules>
            <!--
            Hosting compressed Unity builds without decompression fallback relies on native browser decompression,
            therefore a proper "Content-Encoding" response header should be added for the compressed build files.
 
            NOTE: IIS will throw an exception if the same rule name is used multiple times.
            To avoid possible conflicts with configurations that are already on the server,
            you should remove the mimeType for the corresponding extension using the <remove> element,
            before adding mimeType using the <mimeMap> element.
            -->
 
            <!-- The following section is required for builds compressed with gzip, which don't include decompression fallback. -->
            <remove name="Append gzip Content-Encoding header" />
            <rule name="Append gzip Content-Encoding header">
                <match serverVariable="RESPONSE_Content-Encoding" pattern=".*" />
                <conditions>
                    <add input="{REQUEST_FILENAME}" pattern="\.gz$" />
                </conditions>
                <action type="Rewrite" value="gzip" />
            </rule>
            
            <!-- The following section is required for builds compressed with Brotli, which don't include decompression fallback. -->
            <remove name="Append brotli Content-Encoding header" />
            <rule name="Append brotli Content-Encoding header">
                <match serverVariable="RESPONSE_Content-Encoding" pattern=".*" />
                <conditions>
                    <add input="{REQUEST_FILENAME}" pattern="\.br$" />
                </conditions>
                <action type="Rewrite" value="br" />
            </rule>
        </outboundRules>
    </system.webServer>
</configuration>
```
