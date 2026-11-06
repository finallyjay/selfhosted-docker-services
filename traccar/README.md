# Traccar

Deploys [Traccar](https://www.traccar.org/) GPS tracking
server. Supports multiple tracking protocols on configurable
ports.

## Environment Variables

| Variable                    | Description                        |
|-----------------------------|------------------------------------|
| `TRACCAR_DATABASE_URL`      | JDBC URL of the database           |
| `TRACCAR_DATABASE_USER`     | Database user                      |
| `TRACCAR_DATABASE_PASSWORD` | Database password                  |
| `TRACCAR_NOTIFICATOR_KEY`   | Key for Traccar push notifications |

Traccar is configured through environment variables
(`CONFIG_USE_ENVIRONMENT_VARIABLES=true`), so there is no `traccar.xml`.
A config key becomes a variable by replacing dots with underscores in
screaming snake case (`coordinates.minError` -> `COORDINATES_MIN_ERROR`).
Non-secret settings live in the compose file; secrets come from the
environment.

Do not set `COORDINATES_MAX_ERROR`. In Traccar 6.16+ it snaps every
position farther than that distance from the last stored one back onto
it, valid fixes included. Combined with `FILTER_DISTANCE`, that drops
every position and the device stays frozen in place. Use
`FILTER_MAX_SPEED` to reject GPS jumps instead.

## Volumes

| Volume         | Mount               | Contents  | Backup |
|----------------|---------------------|-----------|--------|
| `traccar-data` | `/opt/traccar/data` | App data  | No     |
| `traccar-logs` | `/opt/traccar/logs` | Log files | No     |

The positions live in the MariaDB database, which is backed up separately.

## Networks

No network is declared in the compose file — Dokploy
attaches its own network automatically on deploy.

## Notes

- Default ports: `25002` (mapped to 5013) and
  `25003` (mapped to 5023).

## Updates

Watchtower updates the `traccar` container automatically, following the
`latest` tag. The MariaDB database is not part of this stack. Dokploy
redeploys the stack when files under `traccar/**` change on `main`.
