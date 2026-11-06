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

## Restoring a backup

Dokploy's built-in restore does not work for MongoDB: it runs
`mongorestore --db <name> --archive`, which current MongoDB tools
reject with `invalid db name`. Restore the backup file by hand.

`--drop` replaces the collections in the backup, so take a fresh
backup first if the current data matters. The backup must come
from the same MongoDB major version (8.x) as the target.

1. Download the backup (`.bson.gz`) from the S3 destination.
2. Stop the API so nothing writes during the restore:
   `docker stop myspotify-api`
3. Restore it with the root user from the container environment:

   ```sh
   docker exec -i myspotify-mongo sh -c 'mongorestore \
     -u "$MONGO_INITDB_ROOT_USERNAME" -p "$MONGO_INITDB_ROOT_PASSWORD" \
     --authenticationDatabase admin --archive --gzip --drop \
     --nsInclude "your_spotify.*"' < backup.bson.gz
   ```

4. Check the summary line (`N document(s) restored successfully.
   0 document(s) failed to restore.`), then start the API again:
   `docker start myspotify-api`

The file has the same format as `mongodump --archive --gzip`, so
the same command restores a manual dump.

## Migrating from the host-path version

Earlier versions stored MongoDB in `${MYSPOTIFY_DATA}/mongo`
without authentication. The new volume starts empty; nothing is
migrated automatically.

1. Stop the old stack and dump the old database:
   `docker exec myspotify-mongo mongodump -d your_spotify --archive --gzip > your_spotify.archive.gz`
2. Deploy this version, so MongoDB creates the root user in the
   new volume.
3. Restore the dump into the new database before using the app,
   as described in [Restoring a backup](#restoring-a-backup)
   (use `your_spotify.archive.gz` instead of `backup.bson.gz`).

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
