# Speedtest Docker Deployment

This directory contains the configuration to deploy the Speedtest server using Docker Compose.

## Structure

- `docker-compose.yml`: Configuration to launch the containers.
- `.env.example`: Example of the necessary environment variables.
- Persistent data at `${SPEEDTEST_DATA}` (configuration files).

## Environment Variables

To use this setup you need to define the following variables in a `.env` file (or directly in Dokploy):

- `SPEEDTEST_DATA`: Local path where Speedtest service data is located.
- `SPEEDTEST_LOCAL_DOMAIN`: Local domain or URL to access the Speedtest web interface.
- `SPEEDTEST_APP_NAME`: The name to display for the Speedtest application instance.
- `SPEEDTEST_APP_KEY`: The secret key used for authenticating requests to the Speedtest server.
- `SPEEDTEST_TELEGRAM_BOT_TOKEN`: The token for your Telegram bot to enable notifications or integrations.

## Docker Networks

The internal Docker network `cloudflare-exposed` must exist on the Docker host before starting the container. This network allows the service to be exposed securely via Cloudflare Tunnel (see the `cloudflared` directory for tunnel setup).

**Networks used:**

- `cloudflare-exposed` (internal)
