# Reverse Proxy

Using a reverse proxy performs better than doing SSL encryption within Unity as part of the game server process:

* Unity is single-threaded, while the reverse proxy is a separate process that's generally engineered to enterprise class performance and is multi-threaded.
* Encryption is computationally expensive, so better to not bog down Unity with that workload.
* Unity / mono may not be up to date on TLS version or be able to read the latest certificates.

This page has instructions for both [Linux](reverse-proxy.md#linux-apache) and [Windows](reverse-proxy.md#windows-iis).

## Linux / Apache

Use nginx - Details coming soon.

## Windows / IIS

Uses Application Request Routing (ARR)...download ARR [here](https://www.microsoft.com/en-us/download/details.aspx?id=47333) and install it.

In IIS Control Panel, select the IIS Server, and open Configuration Editor (near the bottom).

Change the Section selector to `system.webServer/proxy` as shown in this image and set Enabled to True and click Apply (top right).

<figure><img src="../../../.gitbook/assets/image (43).png" alt=""><figcaption><p>Configuration Editor to enable Proxy in IIS</p></figcaption></figure>

### SSL Certificate

You'll need an SSL certificate for your domain.

* Download WinAcme from [here](https://www.win-acme.com/) and unzip it.
* Run WACS.exe and follow the prompts to create a Certificate using default settings for the Default site in IIS.  You'll update the bindings after this step for the game client and proxy.
* WinAcme creates a scheduled task to update the SSL periodically before it expires.

<figure><img src="../../../.gitbook/assets/image (37).png" alt=""><figcaption><p>WinAcme Console Application</p></figcaption></figure>

### Setup Bindings

Under the IIS server, expand Sites, select the Default site, and click Bindings in the right panel.

Set up IIS Bindings as shown below using your domain:

<figure><img src="../../../.gitbook/assets/image (37) (2).png" alt=""><figcaption><p>IIS Bindings</p></figcaption></figure>

### WebGL Client

Deploy your WebGL build to the site folder.  Make sure Simple Web Transport is set up like this:

* Port `7777`
* Client Use Wss checked
* Ssl Enabled **not** checked
* Ssl Protocol set to Tls 13.

<figure><img src="../../../.gitbook/assets/image (39).png" alt=""><figcaption><p>Simple Web Transport Client Settings</p></figcaption></figure>

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
