# ArchiSteamFarm Docker Deployment

This directory contains the configuration to deploy ArchiSteamFarm along with its Cloudflare tunnel using Docker Compose.

## Structure

- `docker-compose.yml`: configuration to launch the containers.
- `.env.example`: example of the necessary environment variables.
- Persistent ArchiSteamFarm data at `${ARCHISTEAMFARM_DATA}` (config and logs).

## Environment Variables

To use this setup you need to define the following variables in a `.env` file (or directly in Dokploy):

- `ARCHISTEAMFARM_DATA`: Local path where ArchiSteamFarm configuration and logs are stored.
- `ARCHISTEAMFARM_LOCAL_DOMAIN`: Local domain or URL to access the ArchiSteamFarm web interface.
- `ARCHISTEAMFARM_CLOUDFLARE_TUNNEL_TOKEN`: Cloudflare tunnel token to enable secure remote access.

## Docker Networks

Two external Docker networks must exist on the Docker host before starting the containers:

- `dokploy-network`
- `archisteamfarm-cf-tunnel`

To create the tunnel network (adjust as needed):

```bash
docker network create \
  --driver ipvlan \
  --subnet 11.0.0.0/29 \
  --ip-range 11.0.0.0/29 \
  --internal \
  archisteamfarm-cf-tunnel
```
