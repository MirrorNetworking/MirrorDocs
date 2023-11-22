# Caddy

[Following the official guide](https://caddyserver.com/docs/quick-starts/reverse-proxy), it's as simple as defining a `Caddyfile`:

```
example.com:7777

reverse_proxy:27777
```

Or running caddy via:

```
caddy reverse-proxy --from example.com:7777 --to :27777
```
