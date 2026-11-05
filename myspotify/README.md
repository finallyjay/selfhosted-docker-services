# MySpotify

Deploys [Your Spotify](https://github.com/Yooooomi/your_spotify)
to track and visualize personal Spotify listening statistics.
Includes three containers: backend API, MongoDB database,
and frontend client.

## Environment Variables

| Variable                   | Description                       |
|----------------------------|-----------------------------------|
| `MYSPOTIFY_DATA`           | Local path for database storage   |
| `MYSPOTIFY_DOMAIN`         | Domain to access the web interface|
| `MYSPOTIFY_BACKEND_DOMAIN` | Domain for the backend API        |
| `MYSPOTIFY_APP_PUBLIC`     | Spotify application public key    |
| `MYSPOTIFY_APP_SECRET`     | Spotify application secret key    |

## Networks

Connects to `cloudflare-exposed`
(must be created by the `cloudflared` service first).

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
