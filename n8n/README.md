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
| `N8N_WEBHOOK_URL`         | Public base URL for webhooks, e.g. `https://…/`   |
| `N8N_NODES_EXCLUDE`       | Optional JSON list of disabled nodes (see below)  |
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
- `N8N_NODES_EXCLUDE` unset keeps n8n's defaults: Execute Command and
  Local File Trigger disabled. Use that for an instance reachable from
  the internet (e.g. one that only serves public webhooks), and create
  its owner account over a private connection before exposing it.
- Setting `N8N_NODES_EXCLUDE=["n8n-nodes-base.localFileTrigger"]`
  enables Execute Command, so workflows can run `git` for the GitHub
  backups. Any workflow can then run commands inside the container and
  read its environment, including `GITHUB_BACKUP_TOKEN`: keep that
  token read-only and keep that instance off the internet.
- The same compose can serve several instances with different
  environments (for example an internal one and a public webhook one),
  each on its own Docker host: `container_name` is fixed to `n8n`.
- Moving an existing SQLite instance into this compose:
  1. Stop the source instance and keep it stopped during the copy.
  2. Copy its `.n8n` folder into the `n8n-data` volume as `/data/.n8n`,
     owned by `1000:1000`.
  3. Set `N8N_ENCRYPTION_KEY` to the `encryptionKey` from its
     `.n8n/config`.
  4. Keep the same public hostname and `N8N_WEBHOOK_URL`, so webhook
     senders need no change; trigger a test webhook afterwards.

  Instances backed by PostgreSQL keep workflows and credentials in the
  database: move them with `n8n export:entities` / `import:entities`
  instead.
- Nodes that read or write files are limited to `/tmp/github-bundles`
  (`N8N_RESTRICT_FILE_ACCESS_TO`), outside the data volume.

## Updates

Watchtower updates the `n8n` container automatically, following the
`latest` tag. Dokploy redeploys the stack when files under `n8n/**`
change on `main`.
