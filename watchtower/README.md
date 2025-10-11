# Watchtower Docker Deployment

This directory contains the configuration to deploy the Watchtower using Docker Compose.

## Structure

- `docker-compose.yml`: Configuration to launch the containers.
- `.env.example`: Example of the necessary environment variables.

## Environment Variables

To use this setup you need to define the following variables in a `.env` file (or directly in Dokploy):

- `WATCHTOWER_HOSTNAME`: Hostname for the instance that is running Watchtower.
- `WATCHTOWER_TELEGRAM_BOT_TOKEN`: Telegram bot token used for notifications.
- `WATCHTOWER_TELEGRAM_CHAT_ID`: Telegram chat ID used by the telegram bot for notifications.