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

## Docker Networks

The internal Docker network `cloudflare-exposed` must exist on the Docker host before starting the container. This network allows the service to be exposed securely via Cloudflare Tunnel (see the `cloudflared` directory for tunnel setup).

**Networks used:**

- `cloudflare-exposed` (internal)
