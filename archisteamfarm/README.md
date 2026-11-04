# ArchiSteamFarm

Deploys [ArchiSteamFarm](https://github.com/JustArchiNET/ArchiSteamFarm)
for Steam account management and automated card farming.

## Environment Variables

| Variable                      | Description                        |
|-------------------------------|------------------------------------|
| `ARCHISTEAMFARM_LOCAL_DOMAIN` | Domain to access the web interface |

## Volumes

| Volume                   | Mount          | Contents                       | Backup |
|--------------------------|----------------|--------------------------------|--------|
| `archisteamfarm-config`  | `/app/config`  | ASF and bot config, sessions   | Yes    |
| `archisteamfarm-plugins` | `/app/plugins` | Installed plugins              | Yes    |
| `archisteamfarm-logs`    | `/app/logs`    | Log files                      | No     |

Named Docker volumes, so they can be backed up with Dokploy volume backups.
With Docker Compose the real names are prefixed with the project name
(for example `<project>_archisteamfarm-config`).

## Networks

Connects to `cloudflare-exposed`
(must be created by the `cloudflared` service first).
