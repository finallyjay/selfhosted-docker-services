# Homepage Docker Deployment

This directory contains the configuration to deploy the Homepage application along with its Cloudflare tunnel using Docker Compose.

## Structure

- `docker-compose.yml`: Configuration to launch the containers.
- `.env.example`: Example of the necessary environment variables.
- Persistent Homepage data at `${HOMEPAGE_DATA}` (configuration files).

## Environment Variables

To use this setup you need to define the following variables in a `.env` file (or directly in Dokploy):

- `HOMEPAGE_DATA`: Local path where Homepage configuration is stored.
- `HOMEPAGE_LOCAL_DOMAIN`: Local domain or URL to access the Homepage web interface.
- `HOMEPAGE_CLOUDFLARE_TUNNEL_TOKEN`: Cloudflare Tunnel token to enable secure remote access.

## Docker Networks

Two external Docker networks must exist on the Docker host before starting the containers:

- `dokploy-network`
- `homepage-cf-tunnel`

To create the tunnel network with the appropriate subnet (`10.0.0.0/29`), run:

```bash
docker network create \
  --driver ipvlan \
  --subnet 10.0.0.0/29 \
  --ip-range 10.0.0.0/29 \
  --internal \
  homepage-cf-tunnel
```
