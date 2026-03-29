# Cloudflared

Runs a [Cloudflare Tunnel][tunnel-docs] to securely expose
internal Docker services to the internet without opening
ports on the host.

This service creates the shared `cloudflare-exposed` bridge
network. Any service that needs external access must join
this network.

## How It Works

1. The `cloudflared` container establishes a tunnel
   using your Cloudflare token.
2. The `cloudflare-exposed` Docker network is created
   and shared across services.
3. Services attached to this network become reachable
   through Cloudflare.
4. Routes and access policies are managed in the
   [Cloudflare Zero Trust dashboard][zero-trust].

## Environment Variables

| Variable                 | Description                            |
|--------------------------|----------------------------------------|
| `CLOUDFLARE_TUNNEL_TOKEN`| Tunnel token from Cloudflare Zero Trust|

## Connecting a Service

Add the `cloudflare-exposed` network to any service
you want to expose:

```yaml
services:
  my-service:
    image: example/my-service:latest
    networks:
      - cloudflare-exposed

networks:
  cloudflare-exposed:
    external: true
```

## Useful Resources

- [Cloudflare Tunnel docs][tunnel-docs]
- [cloudflared GitHub repository][cloudflared-repo]

[tunnel-docs]: https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/
[zero-trust]: https://one.dash.cloudflare.com/
[cloudflared-repo]: https://github.com/cloudflare/cloudflared
