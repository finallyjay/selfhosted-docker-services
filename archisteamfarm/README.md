# ArchiSteamFarm Docker Deployment

This directory contains the configuration to deploy ArchiSteamFarm using Docker Compose, with secure remote access via Cloudflare Tunnel.

## Structure

- `docker-compose.yml`: Configuration to launch the container.
- `.env.example`: Example of the necessary environment variables.
- Persistent ArchiSteamFarm data at `${ARCHISTEAMFARM_DATA}` (config and logs).

## Environment Variables

Define the following variables in a `.env` file (or directly in Dokploy):

- `ARCHISTEAMFARM_DATA`: Local path for ArchiSteamFarm configuration and logs.
- `ARCHISTEAMFARM_LOCAL_DOMAIN`: Local domain or URL to access the ArchiSteamFarm web interface.

## Docker Networks

The internal Docker network `cloudflare-exposed` must exist on the Docker host before starting the container. This network allows ArchiSteamFarm to be exposed securely via Cloudflare Tunnel (see the `cloudflared` directory for tunnel setup).

**Networks used:**

- `cloudflare-exposed` (internal)
