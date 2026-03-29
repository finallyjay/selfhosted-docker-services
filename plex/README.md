# Plex

Deploys [Plex Media Server](https://www.plex.tv/) for
organizing and streaming movies, TV shows and music.
Uses GPU hardware transcoding via `/dev/dri`.

## Environment Variables

| Variable            | Description                            |
|---------------------|----------------------------------------|
| `PLEX_DATA`         | Local path for Plex configuration      |
| `PLEX_MOUNTED_DATA` | Local path where media files are stored|
| `PLEX_LOCAL_IP`     | Local IP of the server running Plex    |
| `PLEX_CLAIM`        | Plex claim token for registration      |
| `PLEX_LOCAL_DOMAIN` | Domain to access the web interface     |

## Notes

- Media volumes are mounted as read-only.
- GPU access (`/dev/dri`) is required for hardware
  transcoding.
