# n8n

Deploys [n8n](https://n8n.io/), a workflow automation tool.
Uses SQLite for storage and bind-mounts a host folder at
`/backups`.

The same `docker-compose.yml` is meant to run as two
independent Dokploy applications on different hosts,
differing only in their environment variables:

- **Host with network storage access:** `N8N_BACKUPS_PATH`
  points to an OS-level NFS automount.
- **Host without it** (e.g. a remote VPS): `N8N_BACKUPS_PATH`
  points to a plain local directory.

## Environment Variables

| Variable             | Description                                             |
|----------------------|---------------------------------------------------------|
| `N8N_DATA`           | Local path for persistent n8n data (mounted at `/data`) |
| `N8N_USER`           | `UID:GID` the container runs as (see Notes for perms)   |
| `N8N_PORT`           | Host port mapped to the container's `5678`              |
| `TZ`                 | Timezone (also used as `GENERIC_TIMEZONE`)              |
| `N8N_HOST`           | Public hostname n8n serves on                           |
| `N8N_PROTOCOL`       | `http` or `https` (used for URLs and cookies)           |
| `N8N_SECURE_COOKIE`  | `true` when serving over HTTPS, `false` otherwise       |
| `N8N_ENCRYPTION_KEY` | Secret used to encrypt stored credentials               |
| `N8N_BACKUPS_PATH`   | Host path bind-mounted at `/backups` (see Notes)        |

## Networks

No network is declared in the compose file — Dokploy
attaches its own network automatically on deploy.

## Volumes

- `${N8N_DATA}/data` → `/data` (bind mount, holds n8n's
  SQLite DB and configuration; `HOME` is set to `/data`).
- `${N8N_BACKUPS_PATH}` → `/backups` (bind mount with
  `rslave` propagation, so an on-demand NFS automount that
  appears on the host *after* the container starts is still
  visible inside it).

## Notes

- `${N8N_DATA}/data` on the host must be owned by the same
  `UID:GID` set in `N8N_USER`, otherwise n8n will fail to
  write its database. The backups folder must also be
  writable by that UID (match `N8N_USER` to the owner of
  the network share, or make the share group-writable).
- **Network-storage resilience:** mount the NFS share via
  the OS, not via a Docker volume, using an fstab line with
  `x-systemd.automount,nofail`, e.g.:

  ```text
  <nas-host>:/<export-path> /mnt/backups-n8n nfs \
    defaults,nofail,x-systemd.automount,_netdev,vers=4.1 0 0
  ```

  This way n8n boots immediately after a power outage even
  if the storage server is slower to come up; autofs mounts
  the share on first access and `rslave` propagation makes
  it visible inside the running container. While the share
  is down, backup operations fail cleanly instead of writing
  to the host's local disk and being silently lost.
- Set `N8N_PROTOCOL=https` and `N8N_SECURE_COOKIE=true`
  only when a reverse proxy terminates TLS in front of
  n8n; for plain HTTP use `http` / `false`.
- Generate `N8N_ENCRYPTION_KEY` once and keep it stable —
  rotating it makes existing stored credentials unreadable.
- `NODE_FUNCTION_ALLOW_BUILTIN=fs` enables the `fs` module
  inside Function nodes (needed if workflows read/write
  files under `/data` or `/backups`).
