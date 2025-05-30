# Traccar Docker Deployment

This directory contains the configuration to deploy the Traccar server using Docker Compose.

## Structure

- `docker-compose.yml`: Configuration to launch the containers.
- `.env.example`: Example of the necessary environment variables.
- Persistent data at `${TRACCAR_DATA}` (configuration files).

## Environment Variables

To use this setup you need to define the following variables in a `.env` file (or directly in Dokploy):

- `TRACCAR_DATA`: Local path where Traccar service data is located.

## Docker Networks

Two external Docker networks must exist on the Docker host before starting the containers:
- `dokploy-network`
