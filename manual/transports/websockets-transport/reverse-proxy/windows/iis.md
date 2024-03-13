# IIS

Once you have your Windows Web Server up and running and RDP access to it, you'll probably need to install a couple optional modules:

* URL Rewrite Module - Download [here](https://iis-umbraco-dr.azurewebsites.net/downloads/microsoft/url-rewrite) and install it.
* Application Request Routing (ARR) - download [here](https://www.microsoft.com/en-us/download/details.aspx?id=47333) and install it.

From the Start menu, type IIS and you'll be able to select Internet Information Services (IIS) Manager.

In IIS Manager, select the server just below Start Page in the left panel, and open Configuration Editor in the Management section near the bottom of the main window.

<div align="left">

<figure><img src="../../../../../.gitbook/assets/image (32).png" alt=""><figcaption><p>IIS Manager - Configuration Editor in Management section</p></figcaption></figure>

</div>

Change the Section selector to `system.webServer/proxy` as shown in this image and set Enabled to True and click Apply (top right).

<div align="left">

<figure><img src="../../../../../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

</div>

### Initial Site Bindings

Now in the left panel of IIS Manager, expand the server and Sites and select the Default Web Site.

* In the right panel, click Bindings.
* In the Bindings window, double-click the single entry that will be there for port 80.
* In the Host name field, type the Fully Qualified Domain Name (FQDN) that you want clients to connect to, and click OK, and then Close the Site Bindings window.
* This will be your domain that you've registered and set up DNS pointing to this web server.

<div align="left">

<figure><img src="../../../../../.gitbook/assets/image (21).png" alt=""><figcaption><p>Edit Site Bindings - Type your FQDN and click OK</p></figcaption></figure>

</div>

This is what it should look like when you're done:

<div align="left">

<figure><img src="../../../../../.gitbook/assets/image (31).png" alt=""><figcaption><p>Site Bindings with Host Name</p></figcaption></figure>

</div>

### App Pool Recycling

Since all clients will be connected through IIS, which uses a "worker process" that will self-recycle by default at a time interval, we need to disable recycling because that would disconnect all clients.

* With the Default Web Site still selected, click Basic Settings in the right panel, and make note of the name of the Application Pool name shown in that window, then close that window.
* In the left panel, click Application Pools and in the main panel select the one that was assigned in Basic Settings above, and click Recycling in the right panel.
* In the Edit Application Pool Recycling Settings, make sure nothing is checked (as shown below), then click Next, and then Finish.

<div align="left">

<figure><img src="../../../../../.gitbook/assets/image (12).png" alt=""><figcaption><p>Edit Application Pool Recycling Settings</p></figcaption></figure>

</div>

### SSL Certificate

{% hint style="warning" %}
NOTE: DNS records pointing to this server need to be done before this step, and propagated to the world DNS servers.  If you can't reach this site with a browser, setting up SSL using WinAcme won't work.
{% endhint %}

You'll need an SSL certificate for your domain.

* Download WinAcme and save it to its own folder from [here](https://www.win-acme.com/) and unzip it to that same folder (makes updating it convenient later as you can safely overwrite with newer versions).
* Run WACS.exe and follow the prompts to create a Certificate using default settings for the Default site in IIS. You'll update the bindings after this step for the game client and proxy.
* WinAcme creates a scheduled task to update the SSL periodically before it expires.

<div align="left">

<figure><img src="../../../../../.gitbook/assets/image (61).png" alt=""><figcaption><p>WinAcme Console Application</p></figcaption></figure>

</div>

### Additional Site Bindings

After successfully installing the SSL with WinAcme, return to IIS Manager, select the Default site, and click Bindings again as you did before.

You'll see that WinAcme has added a binding for port 443 with the same host name you used for port 80.

You can double click the port 443 binding to see how it is set up with the SSL certificate selected.

Click Add to create one more binding for port 7777 as shown below, using the same SSL certificate as was used for port 443.  This will be for the client to connect to the Reverse Proxy that will be set up later in this document. If you're running multiple game server instances on the same server, just add a binding for each port you're using. Make sure your firewall has all of the ports open.

<div align="left">

<figure><img src="../../../../../.gitbook/assets/IIS Bindings.png" alt=""><figcaption><p>IIS Bindings</p></figcaption></figure>

</div>

### Simple Web Transport

Make sure Simple Web Transport is set up like this:

* Ssl Enabled: **not checked**
* Ssl Protocols: **Tls 12**
* Server Port: **27777**
* Client Use Wss: **checked**
* Client Port Option: **Specify Port**
* Custom Client Port: **7777**

<div align="left" data-full-width="false">

<figure><img src="../../../../../.gitbook/assets/image (153).png" alt=""><figcaption><p>Simple Web Transport with Reverse Proxy Settings</p></figcaption></figure>

</div>

### Build and Deploy

In Unity, Network Manager, change Network Address to the same name as you used in Bindings above, e.g. game.example.com.

* Select Dedicated Server platform in Build Settings, build and deploy your server to a folder on the server.  Mirror will start the server on the Port specified (27777 from the image above).
* Change the platform in Build Settings to WebGL, and build the client, and upload that to the folder on your server that your Default website is pointed to in IIS.

### web.config for IIS

In IIS Manager, right-click the Default site that you configured earlier and choose Explore. This is the folder where you'll deploy your WebGL build files.&#x20;

* If there's already a `web.config` file in that folder, open it in Notepad, otherwise create an empty one and open it.
* Merge the XML below into whatever might already be there, save and close it.
* Go back to IIS, select the server itself, and click Restart in the right panel.

The web.config file below assumes several things:

* You've built your server to listen on port `27777` and the WebGL client to connect to `7777`.
* You've built your WebGL client using brotli (preferred) or gzip compression.
* You've set up the bindings as shown above.
* If you made bindings for more servers, you'll need to add additional Reverse Proxy rules to the rules section below, by copy/paste and changing the port in the pattern and action.

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
