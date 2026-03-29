# Speedtest

Deploys [Speedtest Tracker][speedtest-tracker] to run scheduled
internet speed tests and track results over time.
Supports Telegram notifications.

## Environment Variables

| Variable                      | Description                       |
|-------------------------------|-----------------------------------|
| `SPEEDTEST_DATA`              | Local path for config and database|
| `SPEEDTEST_LOCAL_DOMAIN`      | Domain to access the web interface|
| `SPEEDTEST_DOMAIN`            | Public domain for the app URL     |
| `SPEEDTEST_APP_KEY`           | Application secret key            |
| `SPEEDTEST_APP_NAME`          | Display name for the application  |
| `SPEEDTEST_TELEGRAM_BOT_TOKEN`| Telegram bot token for alerts     |

## Networks

Connects to `cloudflare-exposed`
(must be created by the `cloudflared` service first).

[speedtest-tracker]: https://github.com/alexjustesen/speedtest-tracker
