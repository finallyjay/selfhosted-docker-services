# n8n

Deploys [n8n](https://n8n.io/), a workflow automation tool, with SQLite
for storage. Workflows that need to keep files (for example backups)
should send them to object storage such as S3 instead of the local disk.

## Environment Variables

| Variable                  | Description                                       |
|---------------------------|---------------------------------------------------|
| `N8N_PORT`                | Host port mapped to the container's `5678`        |
| `TZ`                      | Timezone (also used as `GENERIC_TIMEZONE`)        |
| `N8N_HOST`                | Hostname n8n serves on                            |
| `N8N_PROTOCOL`            | `http` or `https` (used for URLs and cookies)     |
| `N8N_SECURE_COOKIE`       | `true` when serving over HTTPS, `false` otherwise |
| `N8N_ENCRYPTION_KEY`      | Secret used to encrypt stored credentials         |
| `N8N_GITHUB_BACKUP_TOKEN` | Optional read-only GitHub token for repo backups  |

## Networks

No network is declared in the compose file — Dokploy
attaches its own network automatically on deploy.

## Volumes

| Volume      | Mount          | Contents                                        | Backup |
|-------------|----------------|-------------------------------------------------|--------|
| `n8n-data`  | `/data`        | SQLite database, workflows history, credentials | Yes    |
| `n8n-cache` | `/data/.cache` | UI cache, regenerated on start                  | No     |

Named Docker volumes, so `n8n-data` can be backed up with Dokploy volume
backups. With Docker Compose the real names are prefixed with the project
name (for example `<project>_n8n-data`).

## Notes

- Runs as the image's default `node` user (UID 1000). The `n8n-data`
  volume must be owned by `1000:1000`, otherwise n8n will fail to write
  its database.
- Set `N8N_PROTOCOL=https` and `N8N_SECURE_COOKIE=true` only when a
  reverse proxy terminates TLS in front of n8n; for plain HTTP use
  `http` / `false`.
- Generate `N8N_ENCRYPTION_KEY` once and keep it stable — rotating it
  makes existing stored credentials unreadable.
- The Execute Command node is enabled (`NODES_EXCLUDE` only keeps the
  local file trigger disabled), so workflows can run `git`. Any workflow
  can then run commands inside the container and read its environment,
  including `GITHUB_BACKUP_TOKEN`: keep that token read-only and keep
  n8n off the internet.
- Nodes that read or write files are limited to `/tmp/github-bundles`
  (`N8N_RESTRICT_FILE_ACCESS_TO`), outside the data volume.
