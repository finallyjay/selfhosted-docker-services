# Speedtest

Deploys [Speedtest Tracker][speedtest-tracker] to run scheduled
internet speed tests and track results over time.
Supports Telegram notifications.

## Environment Variables

| Variable                      | Description                       |
|-------------------------------|-----------------------------------|
| `SPEEDTEST_LOCAL_DOMAIN`      | Domain to access the web interface|
| `SPEEDTEST_DOMAIN`            | Public domain for the app URL     |
| `SPEEDTEST_APP_KEY`           | Application secret key            |
| `SPEEDTEST_APP_NAME`          | Display name for the application  |
| `SPEEDTEST_TELEGRAM_BOT_TOKEN`| Telegram bot token for alerts     |

## Volumes

| Volume             | Mount     | Contents                           | Backup |
|--------------------|-----------|------------------------------------|--------|
| `speedtest-config` | `/config` | SQLite database, app keys and logs | Yes    |

Named Docker volume, so it can be backed up with Dokploy volume backups.
With Docker Compose the real name is prefixed with the project name
(for example `<project>_speedtest-config`).

[speedtest-tracker]: https://github.com/alexjustesen/speedtest-tracker

## Updates

Watchtower updates the `speedtest` container automatically, following the
`latest` tag. Dokploy redeploys the stack when files under `speedtest/**`
change on `main`.
