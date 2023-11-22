# NGINX

NGINX is a fast and lightweight web server that can also function as a reverse proxy. It is commonly used for high traffic websites and applications.

Adding a reverse proxy to NGINX is as simple as defining another server in your config. On Debian / Ubuntu this is usually done by creating a file under `/etc/nginx/sites-enabled/`, for this example we'll use `game.conf`

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
        proxy_pass 'http://127.0.0.1:27777';
        
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
