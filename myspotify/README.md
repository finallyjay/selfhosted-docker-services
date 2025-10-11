# MySpotify Docker Deployment

This directory contains the configuration to deploy MySpofity along with its Cloudflare tunnel using Docker Compose.

## Structure

- `docker-compose.yml`: configuration to launch the containers.
- `.env.example`: example of the necessary environment variables.
- Persistent MySpotify data at `${MYSPOTIFY_DATA}` (config and database).

## Environment Variables

To use this setup you need to define the following variables in a `.env` file (or directly in Dokploy):

- `MYSPOTIFY_DATA`: Local path where MySpotify configuration and database are stored.
- `MYSPOTIFY_DOMAIN`: Domain or URL to access the MySpotify web interface.
- `MYSPOTIFY_BACKEND_DOMAIN`: Domain or URL to access the MySpotify backend.
- `MYSPOTIFY_APP_PUBLIC`: Spotify App Public key.
- `MYSPOTIFY_APP_SECRET`: Spotify App Secret key.

## Docker Networks

The internal Docker network `cloudflare-exposed` must exist on the Docker host before starting the container. This network allows the service to be exposed securely via Cloudflare Tunnel (see the `cloudflared` directory for tunnel setup).

**Networks used:**

- `cloudflare-exposed` (internal)
