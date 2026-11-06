# MySpotify

Deploys [Your Spotify](https://github.com/Yooooomi/your_spotify)
to track and visualize personal Spotify listening statistics.
Includes three containers: backend API, MongoDB database,
and frontend client.

## Environment Variables

| Variable                   | Description                        |
|----------------------------|------------------------------------|
| `MYSPOTIFY_DOMAIN`         | Domain to access the web interface |
| `MYSPOTIFY_BACKEND_DOMAIN` | Domain for the backend API         |
| `MYSPOTIFY_APP_PUBLIC`     | Spotify application public key     |
| `MYSPOTIFY_APP_SECRET`     | Spotify application secret key     |
| `MYSPOTIFY_MONGO_USER`     | MongoDB root user                  |
| `MYSPOTIFY_MONGO_PASSWORD` | MongoDB root password (URL-safe)   |

MongoDB creates the root user only on its first start, with an
empty volume. Changing these variables later does not change the
user; update it inside MongoDB as well.

## Volumes

| Volume                 | Mount      | Contents         | Backup  |
|------------------------|------------|------------------|---------|
| `myspotify-mongo-data` | `/data/db` | MongoDB database | Dokploy |

The stack does not back itself up. Configure a Dokploy compose
backup for the `myspotify-mongo` service (database `your_spotify`,
type MongoDB, with the root credentials above) and test a restore.

## Migrating from the host-path version

Earlier versions stored MongoDB in `${MYSPOTIFY_DATA}/mongo`
without authentication. The new volume starts empty; nothing is
migrated automatically.

1. Stop the old stack and dump the old database:
   `docker exec myspotify-mongo mongodump -d your_spotify --archive --gzip > your_spotify.archive.gz`
2. Deploy this version, so MongoDB creates the root user in the
   new volume.
3. Restore the dump into the new database (Dokploy restore, or
   `mongorestore --archive --gzip --drop` with the root
   credentials) before using the app.

## Networks

No network is declared in the compose file — Dokploy
attaches its own network automatically on deploy.
The web interface (port 3000) and the API (port 8080) are
published through Dokploy domains.

## Kernel 7.0 workaround (MongoDB)

MongoDB 8.x refuses to start on Linux kernels 6.19 and newer
unless they are 7.0.14+ / 7.1+
([SERVER-121912](https://jira.mongodb.org/browse/SERVER-121912)).
Ubuntu 24.04 HWE kernels report `7.0.0-N`, so they are rejected.

`myspotify-mongo` sets `GLIBC_TUNABLES=glibc.pthread.rseq=1`,
the community workaround
([docker-library/mongo#765](https://github.com/docker-library/mongo/issues/765)).
It is not endorsed by MongoDB. Remove it once the host runs a
kernel MongoDB accepts.

Moving to `mongo:7` is not a simple retag: 7.0 cannot open 8.x
data files, and `mongodump`/`mongorestore` only supports
restoring into the same major version or FCV. Export the data
with `mongoexport`/`mongoimport` instead.

## Updates

Watchtower updates all three containers automatically: the backend and
frontend follow `latest`, and `myspotify-mongo` follows `8`, so it stays
within MongoDB 8. A new MongoDB major is a manual change to the image tag
and needs a dump and restore (see the kernel workaround notes above).
Dokploy redeploys the stack when files under `myspotify/**` change on
`main`.
