# ArchiSteamFarm

Deploys [ArchiSteamFarm](https://github.com/JustArchiNET/ArchiSteamFarm)
for Steam account management and automated card farming.

## Environment Variables

| Variable                      | Description                                        |
|-------------------------------|----------------------------------------------------|
| `ARCHISTEAMFARM_LOCAL_DOMAIN` | Domain to access the web interface                 |
| `ARCHISTEAMFARM_CRYPTKEY`     | Personal encryption key, passed as `ASF_CRYPTKEY`  |

### Encryption key

Without a personal key ASF falls back to its built-in default, which is
public: bot passwords stored with `PasswordFormat` 1 (AES) can then be
decrypted by anyone who can read the config files. The key is also the salt
for `IPCPassword` hashes (`IPCPasswordFormat` 1 or 2).

The deployment fails if `ARCHISTEAMFARM_CRYPTKEY` is not set. After setting
or changing it, re-encrypt each bot password and re-hash the IPC password
with the new key, using the ASF commands `encrypt AES <password>` and
`hash SCrypt <password>`, and write the results to the config files.

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

## Updates

Watchtower updates the `archisteamfarm` container automatically,
following the `latest` tag. Dokploy redeploys the stack when files under
`archisteamfarm/**` change on `main`.
