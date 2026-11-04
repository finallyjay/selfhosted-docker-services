# Apprise

Deploys the [Apprise API](https://github.com/caronc/apprise-api), a
single endpoint that forwards notifications to many services
(Telegram, Discord, email and others). Other services send their
alerts to Apprise instead of configuring each provider themselves.

## Environment Variables

| Variable               | Description                        |
|------------------------|------------------------------------|
| `APPRISE_LOCAL_DOMAIN` | Domain to access the web interface |

Runs in stateful `simple` mode with the admin web UI enabled
(`APPRISE_ADMIN`), so notification targets can be managed from the
browser and are stored under `/config`.

## Volumes

| Volume           | Mount     | Contents                                  | Backup |
|------------------|-----------|-------------------------------------------|--------|
| `apprise-config` | `/config` | Saved configurations (notification URLs)  | Yes    |
| `apprise-plugin` | `/plugin` | Custom notification plugins               | No     |
| `apprise-attach` | `/attach` | Temporary attachments                     | No     |

Named Docker volumes, so they can be backed up with Dokploy volume backups.
With Docker Compose the real names are prefixed with the project name
(for example `<project>_apprise-config`).

The saved configurations contain the tokens of every notification
target, so treat the config volume and its backups as secrets.

## Networks

Declares no network. Other containers reach it by its container name
(`http://apprise:8000`) on the network Dokploy attaches.

## Security

The API and the admin UI have no authentication of their own. Keep the
service off the internet and, if the local network is not trusted, put
an authentication layer in front of it (for example a reverse proxy
with basic auth).
