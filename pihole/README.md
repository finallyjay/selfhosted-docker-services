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

| Volume           | Mount            | Contents                                         | Backup |
|------------------|------------------|--------------------------------------------------|--------|
| `pihole-etc`     | `/etc/pihole`    | Config (`pihole.toml`), gravity and query log DB | No     |
| `pihole-dnsmasq` | `/etc/dnsmasq.d` | Extra dnsmasq config files                       | No     |

Named Docker volumes. With Docker Compose the real names are prefixed with
the project name (for example `<project>_pihole-etc`).

No volume backup on purpose: most of the space is the query log
(`pihole-FTL.db`), and the configuration can be restored with Pi-hole's
Teleporter or replicated to a second Pi-hole (for example with
nebula-sync).

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
