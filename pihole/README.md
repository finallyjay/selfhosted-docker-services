# Pi-hole

Deploys [Pi-hole](https://pi-hole.net/) for network-wide DNS
management and ad blocking. Requires `NET_ADMIN`, `SYS_TIME`
and `SYS_NICE` capabilities for DNS/DHCP operations.

## Environment Variables

| Variable              | Description                            |
|-----------------------|----------------------------------------|
| `PIHOLE_DATA`         | Local path for Pi-hole config and data |
| `PIHOLE_WEB_PASSWORD` | Password for the web admin interface   |
| `PIHOLE_LOCAL_DOMAIN` | Domain to access the web interface     |

## Networks

Connects to `cloudflare-exposed`
(must be created by the `cloudflared` service first).
