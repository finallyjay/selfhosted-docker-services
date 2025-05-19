# Plex Docker Deployment

This directory contains the configuration to deploy the Plex server using Docker Compose.

## Structure

- `docker-compose.yml`: Configuration to launch the containers.
- `.env.example`: Example of the necessary environment variables.
- Persistent Plex data at `${PLEX_DATA}` (configuration files).
- Mounted Plex data at `${PLEX_MOUNTED_DATA}` (configuration files).

## Environment Variables

To use this setup you need to define the following variables in a `.env` file (or directly in Dokploy):

- `PLEX_LOCAL_IP`: Plex local IP of the server which Plex is located.
- `PLEX_CLAIM`: Plex claim value for this server.
- `PLEX_DATA`: Local path where Plex configuration is stored.
- `PLEX_MOUNTED_DATA`: Local path where Plex media is mounted or stored.
- `PLEX_LOCAL_DOMAIN`: Local domain or URL to access the Plex web interface.

## Docker Networks

Two external Docker networks must exist on the Docker host before starting the containers:
- `dokploy-network`
