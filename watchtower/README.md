# Watchtower

Deploys [Watchtower](https://containrrr.dev/watchtower/) to
automatically update Docker containers. Uses label-based
opt-in — only containers with
`com.centurylinklabs.watchtower.enable=true` are updated.
Sends notifications via Telegram.

## Environment Variables

| Variable                       | Description                     |
|--------------------------------|---------------------------------|
| `WATCHTOWER_HOSTNAME`          | Hostname identifier             |
| `WATCHTOWER_TELEGRAM_BOT_TOKEN`| Telegram bot token for alerts   |
| `WATCHTOWER_TELEGRAM_CHAT_ID`  | Telegram chat ID for alerts     |

## Notes

- Runs daily at 3:00 AM.
- Old images are automatically cleaned up after updates.
- Requires read-write access to the Docker socket.
