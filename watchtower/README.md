# Watchtower

Deploys [Watchtower](https://github.com/nicholas-fedor/watchtower)
(the maintained fork of the archived `containrrr/watchtower`) to
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

- Runs daily at 5:00 AM (host time), after nightly backups and
  unattended OS upgrades with their reboots.
- Old images are automatically cleaned up after updates.
- Requires read-write access to the Docker socket.

## Updates

Watchtower updates itself too: its own container has the opt-in label.
Services pinned to an exact version (for example Garage) are updated
through Dependabot pull requests instead.
