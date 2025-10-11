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

## Docker Networks

The internal Docker network `cloudflare-exposed` must exist on the Docker host before starting the container. This network allows the service to be exposed securely via Cloudflare Tunnel (see the `cloudflared` directory for tunnel setup).

**Networks used:**

- `cloudflare-exposed` (internal)
