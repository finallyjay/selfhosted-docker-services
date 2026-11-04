# Plex

Deploys [Plex Media Server](https://www.plex.tv/) for
organizing and streaming movies, TV shows and music.

## Environment Variables

| Variable            | Description                                        |
|---------------------|----------------------------------------------------|
| `PLEX_MOUNTED_DATA` | Host path where the media folders are mounted      |
| `PLEX_LOCAL_IP`     | Local IP of the server running Plex                |
| `PLEX_CLAIM`        | Optional claim token, only for the first setup     |
| `PLEX_LOCAL_DOMAIN` | Domain to access the web interface                 |

## Volumes

| Volume / path          | Mount                | Contents                                            | Backup |
|------------------------|----------------------|-----------------------------------------------------|--------|
| `plex-config`          | `/config`            | Server settings, databases, watch history, metadata | Yes    |
| `${PLEX_MOUNTED_DATA}` | `/movies`, `/music`… | Media files (read-only bind mounts)                 | No     |

`plex-config` is a named Docker volume, so it can be backed up with Dokploy
volume backups. With Docker Compose the real name is prefixed with the
project name (for example `<project>_plex-config`). Plex is stopped during
the backup so its databases stay consistent.

## Notes

- Media folders are mounted read-only.
- Hardware transcoding needs the host GPU. If the host exposes `/dev/dri`,
  add it to the service:

  ```yaml
  devices:
    - /dev/dri:/dev/dri
  ```
