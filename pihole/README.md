# Pi-hole

Deploys [Pi-hole](https://pi-hole.net/) for network-wide DNS
management and ad blocking. Requires `NET_ADMIN`, `SYS_TIME`
and `SYS_NICE` capabilities for DNS/DHCP operations.

## Environment Variables

| Variable              | Description                            |
|-----------------------|----------------------------------------|
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

Connects to `cloudflare-exposed`
(must be created by the `cloudflared` service first).
