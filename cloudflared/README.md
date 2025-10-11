

# Cloudflare Tunnel for Docker

This directory contains the configuration to create a secure Cloudflare Tunnel using [cloudflared](https://github.com/cloudflare/cloudflared), allowing you to expose internal Docker services to the internet via Cloudflare without opening ports on your local network.

## Purpose

The goal is to run a cloudflared container that creates a tunnel and a shared internal Docker network. Any service you want to expose (e.g., speedtest, plex, etc.) must join this shared network to be accessible externally through Cloudflare.

## How does it work?

1. A cloudflared container starts the tunnel using your Cloudflare token.
2. A single internal Docker network (`cloudflare-exposed`) is defined and shared by all exposed services.
3. Any service you want to expose must be attached to this network.
4. Cloudflare manages external access and security.

## Environment Variables

- `CLOUDFLARE_TUNNEL_TOKEN`: Tunnel token generated in Cloudflare Zero Trust.

## Networks Used

- A single shared internal network: `cloudflare-exposed` (internal).
- An external network (if needed, in case of using Dokploy it's not needed to add it)

## Usage Example

To expose a service through the Cloudflare Tunnel, simply attach it to the shared internal network `cloudflare-exposed` in your service's configuration:

## Example Configuration

```yaml
services:
  my-service:
    image: example/my-service:latest
    networks:
      - cloudflare-exposed

networks:
  cloudflare-exposed:
    internal: true
```

## Notes

- All services to be exposed must be attached to the same shared tunnel network.
- See the official documentation for advanced configuration and route management.

## Useful Resources
- [Cloudflare Tunnel Docs](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/)
- [cloudflared GitHub repository](https://github.com/cloudflare/cloudflared)
