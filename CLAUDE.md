# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code)
when working with code in this repository.

## Overview

A collection of self-hosted Docker services, each
independently deployable via [Dokploy](https://dokploy.com).
Dokploy watches the `main` branch and auto-deploys services
when their watch path (e.g., `service1/**`) matches
changed files.

## Service Structure

Every service follows a strict 3-file convention:

```text
service-name/
├── docker-compose.yml
├── .env.example
└── README.md
```

When adding or modifying services, maintain this structure
exactly.

## Architecture

**Network model:** A single Cloudflare Tunnel
(`cloudflared/`) creates and owns the `cloudflare-exposed`
external bridge network. Services that need internet
exposure connect to this network. Exception: `traccar`
uses `dokploy-network`.

**Container management:** Watchtower handles automatic
image updates using label-based opt-in
(`com.centurylinklabs.watchtower.enable=true`). Homepage
provides a dashboard via Docker label discovery
(`homepage.group`, `homepage.name`, `homepage.icon`, etc.).

**Restart policy:** All services use
`restart: unless-stopped`.

## Conventions

- **Commit messages:**
  `[ServiceName] type: Description`
  (e.g., `[Pihole] fix: Remove Cloudflare tunnel config`)
- **Environment variables:**
  Use `SERVICE_NAME_VARIABLE` naming. Data paths use
  `*_DATA` suffix. Credentials and tokens go in `.env`,
  never committed.
- **`.env.example`:**
  Must list all required variables with placeholder values.
  The actual `.env` file is not tracked.
- **README.md per service:**
  Documents environment variables, networks, and any
  special setup (permissions, dependencies).
- **Volume mounts:**
  Persistent data paths are configurable via env vars.
  Use read-only mounts (`:ro`) where appropriate
  (e.g., `/etc/localtime`).
- **docker-compose property order:**
  All services must follow this order
  (skip properties that don't apply):
  `image` > `container_name` > `hostname` > `restart` >
  `command` > `depends_on` > `devices`/`cap_add` >
  `security_opt` > `dns`/`dns_search` > `healthcheck` >
  `networks` > `ports` > `volumes` > `environment` >
  `logging` > `labels`
