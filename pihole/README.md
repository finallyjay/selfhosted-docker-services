# Pi-hole

Deploys [Pi-hole](https://pi-hole.net/) for network-wide DNS
management and ad blocking. Requires `NET_ADMIN`, `SYS_TIME`
and `SYS_NICE` capabilities for DNS/DHCP operations.

## Environment Variables

| Variable              | Description                            |
|-----------------------|----------------------------------------|
| `PIHOLE_HOSTNAME`     | Container hostname                     |
| `PIHOLE_WEB_PASSWORD` | Password for the web admin interface   |
| `PIHOLE_LOCAL_DOMAIN` | Domain to access the web interface     |

## Volumes

| Volume           | Mount            | Contents                                         | Backup          |
|------------------|------------------|--------------------------------------------------|-----------------|
| `pihole-etc`     | `/etc/pihole`    | Config (`pihole.toml`), gravity and query log DB | No (see below)  |
| `pihole-dnsmasq` | `/etc/dnsmasq.d` | Extra dnsmasq config files                       | No              |
| `pihole-backup`  | `/backup`        | Daily Teleporter exports (last 7)                | Yes             |

Named Docker volumes. With Docker Compose the real names are prefixed with
the project name (for example `<project>_pihole-etc`).

## Backup

`pihole-etc` is not backed up as a volume: most of its space is the query
log (`pihole-FTL.db`, over 1 GB), and a consistent copy would need the
container stopped. Instead, a daily Teleporter export (config, gravity,
dnsmasq files and hosts, about 40 KB) is written to `pihole-backup`, and
only that volume is backed up.

In Dokploy:

1. **Schedule** on the `pihole` service (shell `sh`), before the backup:

   ```sh
   cd /backup && pihole-FTL --teleporter >/dev/null && ls -1t pi-hole_*_teleporter_*.zip | tail -n +8 | xargs -r rm -f
   ```

   Without an argument, `pihole-FTL --teleporter` **exports** a zip to the
   current directory. With a file argument it **imports** that file, so
   never pass one here.

2. **Volume backup** of `pihole-backup`, without stopping the container
   (the zips are written once and never change).

To restore, upload the latest zip in the web UI (Settings → Teleporter),
or copy it into the container and run `pihole-FTL --teleporter <file>`.
The query history is not restored.

## Networks

Declares no networks. Dokploy attaches `dokploy-network` on deploy
because the service has a domain; Traefik reaches it there.
Keep a Dokploy domain on this service: without one, Dokploy does not
attach `dokploy-network` and the Homepage widget (`http://pihole`)
can no longer reach it.

## Updates

Watchtower updates the `pihole` container automatically, following the
`latest` tag. Dokploy redeploys the stack when files under `pihole/**`
change on `main`.
