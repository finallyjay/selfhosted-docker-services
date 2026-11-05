# qBittorrent

Deploys [qBittorrent](https://www.qbittorrent.org/) (web UI) as the torrent
client. Downloads land on the local disk while in progress and are moved to
the NAS when complete; seeding then reads from the NAS.

## Environment Variables

| Variable                       | Description                                         |
|--------------------------------|-----------------------------------------------------|
| `QBITTORRENT_PEER_PORT`        | Peer port, TCP and UDP (forward it on the router)   |
| `QBITTORRENT_NAS_ADDRESS`      | NAS address (NFS server)                            |
| `QBITTORRENT_MEDIA_EXPORT`     | NFS export of the media library                     |
| `QBITTORRENT_DOWNLOADS_EXPORT` | NFS export of the general downloads folder          |
| `QBITTORRENT_INCOMPLETE_DATA`  | Host path, on local disk, for downloads in progress |
| `QBITTORRENT_LOCAL_DOMAIN`     | Local domain of the web UI                          |

## Volumes

| Volume / path                    | Mount         | Contents                                       | Backup |
|----------------------------------|---------------|------------------------------------------------|--------|
| `qbittorrent-config`             | `/config`     | Settings, categories, torrents and resume data | Yes    |
| `qbittorrent-media`              | `/media`      | Media library (NFS volume)                     | No     |
| `qbittorrent-downloads`          | `/downloads`  | General downloads (NFS volume)                 | No     |
| `${QBITTORRENT_INCOMPLETE_DATA}` | `/incomplete` | Downloads in progress (local disk)             | No     |

## Ports

| Port                       | Protocol | Use                                    |
|----------------------------|----------|----------------------------------------|
| `${QBITTORRENT_PEER_PORT}` | TCP, UDP | Incoming peers (forward on the router) |
| `8080`                     | TCP      | Web UI, published only through Traefik |

## Notes

- **Pinned version.** The image tag is pinned and the service is not opted
  in to Watchtower: private trackers blacklist client versions, so update
  only after checking each tracker's client list.
- **libtorrent v1.** The `-libtorrentv1` variant is used because the data
  lives on the NAS over NFS, where libtorrent 2.0's memory-mapped I/O is
  known to behave poorly. Trackers see the same client either way.
- **NFS.** `/media` and `/downloads` are NFS volumes that Docker mounts
  itself, so the container does not start if the NAS is unreachable instead
  of silently writing downloads to empty local folders. Both exports must be
  read-write for this host (squash to a user that can write them). Plex keeps
  mounting the media library read-only. If the export or address changes,
  remove the volume (`docker volume rm <project>_qbittorrent-media`) so
  Docker recreates it with the new options.
- **First-run settings** (web UI, *Options*):
  - *Downloads*: enable **"Keep incomplete torrents in"** `/incomplete`
    (it is off by default, so downloads would otherwise be written straight
    to the NAS), and use **Automatic** torrent management so each category
    moves its torrents to its own folder.
  - *Categories*: one per destination, each with its save path under
    `/media` or `/downloads` (e.g. movies, series, music, general).
  - *Connection*: the listening port is `QBITTORRENT_PEER_PORT`.
