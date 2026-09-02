# Self-Hosted Docker Services

[![CI](https://github.com/finallyjay/selfhosted-docker-services/actions/workflows/lint-markdown.yml/badge.svg)](https://github.com/finallyjay/selfhosted-docker-services/actions/workflows/lint-markdown.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

A collection of self-hosted services running as independent
Docker Compose stacks, deployed via [Dokploy](https://dokploy.com).

## Services

| Service | Description |
| --- | --- |
| [ArchiSteamFarm](archisteamfarm/) | Steam account management |
| [Cloudflared](cloudflared/) | Cloudflare Tunnel |
| [Homepage](homepage/) | Dashboard and service portal |
| [MySpotify](myspotify/) | Spotify listening statistics |
| [Pi-hole](pihole/) | DNS and ad blocking |
| [Plex](plex/) | Media server |
| [Speedtest](speedtest/) | Internet speed tracking |
| [Traccar](traccar/) | GPS tracking server |
| [Watchtower](watchtower/) | Automatic container updates |

## Prerequisites

- Docker and Docker Compose
- [Dokploy](https://dokploy.com) (optional, for automated deployments)

## Getting Started

1. Clone the repository.
2. Navigate to the service directory you want to deploy.
3. Copy `.env.example` to `.env` and fill in your values.
4. Run `docker compose up -d`.

> Each service is self-contained. You can deploy them independently
> in any order, except that **cloudflared** should be running first
> if the service connects to the `cloudflare-exposed` network.

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
