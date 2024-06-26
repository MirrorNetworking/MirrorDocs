# NGINX

NGINX is a fast and lightweight web server that can also function as a reverse proxy. It is commonly used for high traffic websites and applications.

Adding a reverse proxy to NGINX is as simple as defining a `server` directive definition. On Debian / Ubuntu this is usually done by creating a new file under `/etc/nginx/sites-enabled/` or `/etc/nginx/conf.d/` (double-check the NGINX `include` directive defined in the file `/etc/nginx/nginx.conf`). For this document we're going to assume NGINX is configured to include files from `/etc/nginx/sites-enabled/`.

For this document we're going to explore writing NGINX configuration for WebGL game clients and Mirror game servers.

***

It is worth noting that a the main `/etc/nginx/nginx.conf` file includes files from `/etc/nginx/sites-enabled/` into its `http` directive. The instruction to do this looks something like the following:

```nginx
# ...
http {
    # ...
    include /etc/nginx/sites-enabled/*.conf;
    # ...
}
# ...

```

This is relevant if you are trying to follow online guides as sometimes content authors anticipate you writing a new `nginx.conf` file and other content authors anticipate you writing new `*.conf` files in `sites-enabled`. A clue is if their code sample shows `http`, which is a directive used for TCP connections, and if `http` is in the code sample then you can't use the NGINX configuration without modification in the `sites-enabled` directory because an `http` directive cannot exist in an `http` block.

If you wanted to set up an NGINX server to reverse proxy UDP connections (for example if you are using KcpTransport) then you'd want to add a `stream` directive. Furthermore, if you wanted to set up a similar mechanism as `sites-enabled` but for the `stream` directive you can update the `nginx.conf` file as follows:

```nginx
# ...
http {
    # ...
}
stream {
    include /etc/nginx/streaming/*.conf;
}
# ...

```

For now UDP connections will be out of scope for this document as this document exists to support the Simple Web Transport but hopefully this serves as a good starting point if you are interested in NGINX UDP connections.

### WebGL Game Client NGINX Configuration

To serve the WebGL game client we must keep in mind that from the Unity Editor we can build Brotli (`.br`) compressed files, GZIP (`.gz`) compressed files, or we can build WebGL game clients without compression. In order to serve either `.br` or `.gz` compressed files in our web server we must pass back proper `Content-Encoding` header with either `br` or `gzip` and we should pass back the appropriate `Content-Type` header for the file.

You can follow [Unity's Server Configuration Documentation](https://docs.unity3d.com/Manual/webgl-server-configuration-code-samples.html) but I prefer this configuration. This configuration expects you to put your webGL game client build in `/usr/share/nginx/html`.

Note: the `server_name` is set to `localhost` instead of your domain; this NGINX server will be used later.

`/etc/nginx/sites-enabled/webgl-game-client.conf`

```nginx
map $request_uri $gzip_encoding {
    ~*\.gz$  "gzip";
}
map $request_uri $br_encoding {
    ~*\.br$  "br";
}
map $request_uri $type_application_octet_stream {
    ~*\.data\.(gz|br)$  "application/octet-stream";
}
map $request_uri $type_text_javascript {
    ~*\.framework.js\.(gz|br)$  "text/javascript";
}
map $request_uri $type_application_wasm {
    ~*\.wasm\.(gz|br)$  "application/wasm";
}
server {
    # here we set the domain
    # server_name example.org;
    server_name localhost;
    
    # nginx 1.25.1 and above:
    #   http2 on;
    #   listen 8080;
    #   listen [::]:8080;
    # nginx below 1.25.1:
    listen 8080 http2;
    listen [::]:8080 http2;

    root /usr/share/nginx/html;

    # Web Game Client
    location / {
        try_files $uri $uri/ /index.html =404;
    }

    location ~* \.(data|framework.js|wasm)\.(gz|br)?$ {
        gzip off;
        default_type '';

        add_header Content-Encoding $gzip_encoding always;
        add_header Content-Encoding $br_encoding always;
        add_header Content-Type $type_application_octet_stream always;
        add_header Content-Type $type_text_javascript always;
        add_header Content-Type $type_application_wasm always;
    }
}
```

### NGINX Configuration for a Mirror Game Server

Now that we have accounted for the WebGL game client we can focus on appropriate NGINX configuration for the Mirror Game Server. The expectation is that you will produce a "Dedicated Server" with the _Target Platform_ of "Linux" as your Mirror Game Server. Starting the Mirror game server should be as simple as executing your produced x86\_64 binary (e.g. `./mirror-game-server.x86_64` assuming a build name of `mirror-game-server`) after uploading all game server files. The expectation is that the Mirror game server is run on the same server as NGINX; thus requests are proxied locally (`127.0.0.1`).

The Mirror Game Server exposes an HTTP endpoint to allow client websocket connections (`ws://` protocol) or secure websocket connections (`wss://`) to use. When you start the Mirror Game Server with Simple Web Transport it will listen for these connections on the port defined by SWT.

Our goal with the NGINX configuration is to expose an endpoint on a different port than SWT and proxy requests to the Mirror Game Server HTTP endpoint.

The following config defines a reverse proxy listening on port 7778 to proxy to 127.0.0.1:27778.

`/etc/nginx/sites-enabled/mirror-game-server-single.conf`

<pre class="language-nginx"><code class="lang-nginx"># helper map to support connection upgrading, only define this once
map $http_upgrade $connection_upgrade {
    default upgrade;
    ''      close;
}
# the actual reverse proxy server block
server {
<strong>    # the server will listen on port 7778 for both ipv4 and ipv6 
</strong>    # nginx version 1.25.1 and above:
    #  http2 on;
    #  listen 7778;
    #  listen [::]:7778;
    # nginx below version 1.25.1
    listen 7778 http2;
    listen [::]:7778 http2;
    server_name localhost;
    
    location / {
        proxy_pass 'http://127.0.0.1:27778';
        
        proxy_redirect off;
        # very long timeouts to make sure long-running connections aren't interrupted
        # you might be able to reduce these based on your use-case
        proxy_read_timeout 7d;
        proxy_send_timeout 7d;
        
        # The following headers are general settings, not directly used by SWT
        # Tell upstream the host
        proxy_set_header Host $host;
        # Tell upstream real ip &#x26; forwarded for header
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
</code></pre>

### Reverse Proxy for Mirror Game Server(s) and a WebGL Game Client (SSL Enabled & SSL Termination)

The following config defined a reverse proxy listening on port 8443 to serve WebGL game client and proxy to a Mirror game server. The `upstream` directive is used as an alias for the _web game client_ server and the _mirror game server_ server. Note: the `upstream` directive can be used to load balance requests across multiple servers as demonstrated in comments in the sample config.

SSL termination is the concept that a player interacts over HTTPS or WSS with a server configured to handle those requests (meaning certificates are configured with the server) but then the server proxies the requests to other servers over HTTP or WS. This configuration is common though to achieve "encryption in transit" SSL termination should not be used.

`/etc/nginx/sites-enabled/reverse-proxy.conf`

```nginx
# define an upstream server to proxy websocket connection requests to local (aka 127.0.0.1) game server(s)
upstream mirror_game_server {
    server 127.0.0.1:27778;
    # 27778 is the default port for Mirror Game Server's Simple Web Transport -- adjust accordingly
    # If you produce more Mirror Game Server builds with different SWT ports you may do something like...
    # server 127.0.0.1:27779;
    # server 127.0.0.1:27780;
    # server 127.0.0.1:27781;
}
# the webgl game client uses the server defined by web-game-client.conf
# an alternative setup has this reverse proxy simple serve client files
upstream webgl_game_client {
    server 127.0.0.1:8080;
}
server {
    # here we set the domain (example.org)
    server_name example.org;
    ssl_certificate /etc/letsencrypt/live/example.org/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.org/privkey.pem;
    
    # the server will listen on port 8443 for both ipv4 and ipv6 
    
    # nginx version 1.25.1 and above:
    #    http2 on;
    #    listen 8443 ssl;
    #    listen [::]:8443 ssl;
    # nginx below version 1.25.1
    listen 8443 ssl http2;
    listen [::]:8443 ssl http2;
    # webgl game client served at /client (cannot be served at / as currently the / path is reserved for websocket connection)
    location ~* ^/client(/.*)$ {
        # remove /client from the request path before proxying to the web game client
        rewrite ^/client(/.*)$ $1 break;

        # SSL termination; proxy to http protocol (instead of https)
        proxy_pass http://webgl_game_client;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-Proto https;
        proxy_connect_timeout 10;
    }
    
    # mirror game server websocket proxy
    location / {
        # SSL termination; proxy to http protocol (instead of https)
        proxy_pass http://mirror_game_server;

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
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";

        proxy_buffer_size 2k;
        proxy_buffering off;
        
        # 10s connect timeout
        proxy_connect_timeout 10;
    }
}
```
