# Self-Hosted Docker Services

[![CI](https://github.com/finallyjay/selfhosted-docker-services/actions/workflows/lint-markdown.yml/badge.svg)](https://github.com/finallyjay/selfhosted-docker-services/actions/workflows/lint-markdown.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

A collection of self-hosted services running as independent
Docker Compose stacks, deployed via [Dokploy](https://dokploy.com).

## Services

| Service                           | Description                        | Backup                               |
|-----------------------------------|------------------------------------|--------------------------------------|
| [Apprise](apprise/)               | Notification gateway               | Daily (config volume)                |
| [ArchiSteamFarm](archisteamfarm/) | Steam account management           | Daily (config and plugins volumes)   |
| [Cloudflared](cloudflared/)       | Cloudflare Tunnel                  | Not needed (stateless)               |
| [Garage](garage/)                 | S3-compatible storage for backups  | No (it is the backup target)         |
| [Homepage](homepage/)             | Dashboard and service portal       | Daily (config volume)                |
| [MySpotify](myspotify/)           | Spotify listening statistics       | Daily (MongoDB dump)                 |
| [n8n](n8n/)                       | Workflow automation                | Daily (data volume)                  |
| [Pi-hole](pihole/)                | DNS and ad blocking                | No (by choice)                       |
| [Plex](plex/)                     | Media server                       | Weekly (config volume)               |
| [qBittorrent](qbittorrent/)       | Torrent client                     | Daily (config volume)                |
| [Speedtest](speedtest/)           | Internet speed tracking            | Daily (config volume)                |
| [Traccar](traccar/)               | GPS tracking server                | Daily (database, managed separately) |
| [Watchtower](watchtower/)         | Automatic container updates        | Not needed (stateless)               |
| [WGDashboard](wgdashboard/)       | WireGuard VPN server and dashboard | Daily (data and config volumes)      |

Backups are Dokploy backups to S3-compatible storage, configured outside
this repository.

## Prerequisites

- Docker and Docker Compose
- [Dokploy](https://dokploy.com) (optional, for automated deployments)

## Getting Started

1. Clone the repository.
2. Navigate to the service directory you want to deploy.
3. Copy `.env.example` to `.env` and fill in your values.
4. Run `docker compose up -d`.

> Each service is self-contained. You can deploy them independently
> in any order.

## Deployment via Dokploy

Dokploy integrates with GitHub and watches the `main` branch.
Each service is configured with a **watch path**
(e.g., `pihole/**`) so that only the affected service
is redeployed when its files change.

## Repository Structure

```text
service-name/
├── docker-compose.yml      # Service definition
├── .env.example            # Required environment variables
└── README.md               # Service-specific documentation
```

## License

[MIT](LICENSE)
