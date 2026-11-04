# Garage

Deploys [Garage](https://garagehq.deuxfleurs.fr/), a lightweight
S3-compatible object store, as a single node. It is the destination
for Dokploy database and volume backups.

## Environment Variables

| Variable                 | Description                                       |
|--------------------------|---------------------------------------------------|
| `GARAGE_S3_BIND_ADDRESS` | Host address the S3 API (port 3900) listens on    |
| `GARAGE_META_DATA`       | Host path for metadata (LMDB), on a local disk    |
| `GARAGE_DATA`            | Host path for object data                         |
| `GARAGE_SNAPSHOTS_DATA`  | Host path for metadata snapshots                  |
| `GARAGE_RPC_SECRET`      | RPC secret (`openssl rand -hex 32`)               |
| `GARAGE_ADMIN_TOKEN`     | Admin API token (`openssl rand -base64 32`)       |
| `GARAGE_METRICS_TOKEN`   | Metrics endpoint token (`openssl rand -base64 32`)|

The configuration (`garage.toml`) is defined inline in the compose file
and holds no secrets: Garage reads them from the variables above.
The deploy fails if any of them is missing.

## Storage

| Host path               | Mount                       | Contents                      | Backup              |
|-------------------------|-----------------------------|-------------------------------|---------------------|
| `GARAGE_META_DATA`      | `/var/lib/garage/meta`      | Metadata database (LMDB)      | Snapshots every 6 h |
| `GARAGE_DATA`           | `/var/lib/garage/data`      | Object data                   | No (it is the data) |
| `GARAGE_SNAPSHOTS_DATA` | `/var/lib/garage/snapshots` | Metadata snapshots            | No                  |

Bind mounts instead of named volumes, so data and snapshots can live on
a NAS mount. Keep the metadata on a local disk: LMDB over NFS is slow
and unsafe.

## Networks

Declares no network. The S3 API is published only on
`GARAGE_S3_BIND_ADDRESS`; the admin API (3903) and RPC (3901) are not
published. If that address belongs to a VPN interface such as
Tailscale, Docker must start after it, or the port binding fails at
boot.

## First Setup

On a fresh node, assign the layout and create a bucket and a key:

```sh
docker exec garage /garage status                     # note the node ID
docker exec garage /garage layout assign -z home -c 100G <node-id>
docker exec garage /garage layout apply --version 1
docker exec garage /garage bucket create backups
docker exec garage /garage key create backups-key
docker exec garage /garage bucket allow --read --write backups --key backups-key
```

## Updates

Garage publishes only exact version tags (no `latest` or `v2`), so
Watchtower cannot update it. Dependabot opens a pull request for each
new 2.x release instead; major versions need a manual migration and are
ignored.
