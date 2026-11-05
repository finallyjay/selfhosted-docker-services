# qBittorrent

Deploys [qBittorrent](https://www.qbittorrent.org/) (web UI) as the torrent
client. Downloads land on the local disk while in progress and are moved to
the NAS when complete; seeding then reads from the NAS.

## Environment Variables

| Variable                      | Description                                         |
|-------------------------------|-----------------------------------------------------|
| `QBITTORRENT_PEER_PORT`       | Peer port, TCP and UDP (forward it on the router)   |
| `QBITTORRENT_MEDIA_DATA`      | Host path of the NAS media library (read-write)     |
| `QBITTORRENT_DOWNLOADS_DATA`  | Host path of the NAS general downloads folder       |
| `QBITTORRENT_INCOMPLETE_DATA` | Host path, on local disk, for downloads in progress |
| `QBITTORRENT_LOCAL_DOMAIN`    | Local domain of the web UI                          |

## Volumes

| Volume / path                    | Mount         | Contents                                       | Backup |
|----------------------------------|---------------|------------------------------------------------|--------|
| `qbittorrent-config`             | `/config`     | Settings, categories, torrents and resume data | Yes    |
| `${QBITTORRENT_MEDIA_DATA}`      | `/media`      | Media library (NFS from the NAS)               | No     |
| `${QBITTORRENT_DOWNLOADS_DATA}`  | `/downloads`  | General downloads (NFS from the NAS)           | No     |
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
- **NFS.** The media and downloads folders must be exported read-write to
  this host. Plex keeps mounting the media library read-only.
- **Categories.** Set in the web UI, each with its save path under `/media`
  or `/downloads` (e.g. movies, series, music, general), with downloads in
  progress kept in `/incomplete`.
