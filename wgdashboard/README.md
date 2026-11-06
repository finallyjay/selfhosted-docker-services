# WGDashboard

Deploys [WGDashboard](https://github.com/WGDashboard/WGDashboard), a
WireGuard server with a web UI to manage peers. Useful as a fallback
VPN when the main remote access (for example Tailscale) is unavailable.

## Environment Variables

| Variable                   | Description                                 |
|----------------------------|---------------------------------------------|
| `WGDASHBOARD_LOCAL_DOMAIN` | Domain to access the web interface          |
| `WGDASHBOARD_UI_PORT`      | Host TCP port for the web UI (10086 inside) |
| `WGDASHBOARD_WG_PORT`      | Host UDP port for WireGuard (51820 inside)  |

Forward `WGDASHBOARD_WG_PORT/udp` on the router so clients can connect
from outside. Do not expose the web UI port to the internet.

## Volumes

| Volume              | Mount                     | Contents                                          | Backup |
|---------------------|---------------------------|---------------------------------------------------|--------|
| `wgdashboard-conf`  | `/etc/wireguard`          | WireGuard config (`wg0.conf`, server private key) | Yes    |
| `wgdashboard-data`  | `/data`                   | Dashboard settings and databases (peers, jobs)    | Yes    |
| `wgdashboard-aconf` | `/etc/amnezia/amneziawg`  | AmneziaWG config, if used                         | No     |

Named Docker volumes, so they can be backed up with Dokploy volume backups.
With Docker Compose the real names are prefixed with the project name
(for example `<project>_wgdashboard-conf`).

The config volume holds the server private key and the peers' preshared
keys, so treat it and its backups as secrets.

## Networks

Declares no network. Needs the `NET_ADMIN` capability to manage the
WireGuard interface.

## Updates

Watchtower updates the `wgdashboard` container automatically, following
the `latest` tag. Dokploy redeploys the stack when files under
`wgdashboard/**` change on `main`.
