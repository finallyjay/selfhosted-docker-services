# Cloudflared

Runs a [Cloudflare Tunnel][tunnel-docs] to securely expose
internal Docker services to the internet without opening
ports on the host.

The tunnel joins `dokploy-network`, which Dokploy provides
automatically on deploy, and sends every public hostname to
Traefik (`http://dokploy-traefik`). Traefik then routes the
request using the domain configured in Dokploy.

## How It Works

1. The `cloudflared` container establishes a tunnel
   using your Cloudflare token.
2. Each public hostname is added to the tunnel in the
   [Cloudflare Zero Trust dashboard][zero-trust], pointing
   to `http://dokploy-traefik`.
3. Traefik forwards the request to the service that has
   that domain in Dokploy (HTTPS off, certificate None:
   Cloudflare terminates TLS).
4. Access policies are managed in Cloudflare Zero Trust.

## Environment Variables

| Variable                 | Description                            |
|--------------------------|----------------------------------------|
| `CLOUDFLARE_TUNNEL_TOKEN`| Tunnel token from Cloudflare Zero Trust|

## Exposing a Service

A service is exposed by configuration, not by Docker
networks:

1. Add the domain to the service in Dokploy.
2. Unless it must be public, create its Cloudflare Access
   application first, so the hostname is never reachable
   without it.
3. Add the hostname to the tunnel, pointing to
   `http://dokploy-traefik`.

No network needs to be declared in the service's compose
file.

Traefik's ports 80 and 443 must not be reachable from the
internet. Otherwise a client could send a protected hostname
straight to Traefik and skip Cloudflare Access. On
tuckfow-cloud-01 the Hetzner firewall blocks them. At home
the router forwards no ports to the server.

## Useful Resources

- [Cloudflare Tunnel docs][tunnel-docs]
- [cloudflared GitHub repository][cloudflared-repo]

[tunnel-docs]: https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/
[zero-trust]: https://one.dash.cloudflare.com/
[cloudflared-repo]: https://github.com/cloudflare/cloudflared

## Updates

Watchtower updates the `cloudflare-tunnel` container automatically,
following the `latest` tag. Dokploy redeploys the stack when files under
`cloudflared/**` change on `main`. While the tunnel restarts, every
service exposed through it is briefly unreachable.
