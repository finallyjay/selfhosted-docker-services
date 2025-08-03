# Pi-hole Docker Deployment

This directory contains the configuration to deploy the Pi-hole server using Docker Compose.

## Structure

- `docker-compose.yml`: Configuration to launch the containers.
- `.env.example`: Example of the necessary environment variables.
- Persistent data at `${PIHOLE_DATA}` (configuration files).

## Environment Variables

To use this setup you need to define the following variables in a `.env` file (or directly in Dokploy):

- `PIHOLE_DATA`: Local path where Pi-hole service data is located.
- `PIHOLE_WEB_PASSWORD`: Password to access the Pi-hole web interface.
- `PIHOLE_LOCAL_DOMAIN`: Local domain or URL to access the Pi-hole web interface.
- `PIHOLE_CLOUDFLARE_TUNNEL_TOKEN`: Cloudflare tunnel token to enable secure remote access.

## Docker Networks

Two external Docker networks must exist on the Docker host before starting the containers:
- `dokploy-network`
- `pihole-cf-tunnel`

To create the tunnel network (adjust as needed):

```bash
docker network create \
  --driver ipvlan \
  --subnet 12.0.0.0/29 \
  --ip-range 12.0.0.0/29 \
  --internal \
  pihole-cf-tunnel
```
