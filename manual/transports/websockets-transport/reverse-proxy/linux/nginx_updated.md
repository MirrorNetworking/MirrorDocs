# NGINX (updated)

The previous guide for creating a reverse proxy with nginx is a bit outdated. Here's a guide on making it work in 2024.

This guide assumes you have the server port set to 27777 and the client port set to 7777.

---

Before going through this tutorial, be sure to have SSL setup on the server. This can be done by running [certbot](https://certbot.eff.org/instructions).

---

We will be adding nginx configuration files to the `/etc/nginx/sites-enabled/` directory. Please be sure 

```nginx configuration
# ...
http {
    # ...
    include /etc/nginx/sites-enabled/*.conf;
    # ...
}
# ...

```

---

### WebGL Build

By default, nginx serves the `/var/www/html/` directory. The build files (`Build/`, `TemplateData/`, and `index.html`) can be placed in this directory to serve them from this server. However, if this server will only be used for hosting the dedicated game server, then you can ignore this step.

For this tutorial, we will not use any compression methods. To disable compression in the build, go to `Project Settings/Player/Publishing Settings` and set `Compression Format` to `Disabled`.

### Game Server

We will now setup the game server.

Replace `{domain}` with your domain.

`/etc/nginx/sites-enabled/mirror-game-server-single.conf`
```nginx configuration
# helper map to support connection upgrading, only define this once
map $http_upgrade $connection_upgrade {
    default upgrade;
    ''      close;
}
# the actual reverse proxy server block
server {
    server_name {domain};
    ssl_certificate /etc/letsencrypt/live/{domain}/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/{domain}/privkey.pem;

    # the reverse proxy will listen on port 7777 for both ipv4 and ipv6 
    listen 7777 ssl http2;
    listen [::]:7777 ssl http2;
    
    server_name localhost;
    
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

### Reverse Proxy

Replace `{domain}` with your domain.

`/etc/nginx/sites-enabled/reverse-proxy.conf`
```nginx configuration
# define an upstream server to proxy websocket connection requests to local (aka 127.0.0.1) game server(s)
upstream mirror_game_server {
    server 127.0.0.1:27777; # We will use port 27777 for hosting the server
    # if you produce more Mirror Game Server builds with different SWT ports you may do something like...
    # server 127.0.0.1:27778;
    # server 127.0.0.1:27779;
    # server 127.0.0.1:27780;
}
server {
    # here we set the domain (example.org)
    server_name {domain};
    ssl_certificate /etc/letsencrypt/live/{domain}/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/{domain}/privkey.pem;
    
    # the server will listen on port 8443 for both ipv4 and ipv6 
    listen 8443 ssl http2;
    listen [::]:8443 ssl http2;
    
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

### Deploying

Before deploying the changes, test the config files with `nginx -t`.

If the tests are ok, redploy the nginx server with `sudo systemctl restart nginx`.

Now, the client should be able to connect to `{domain}:7777`.
