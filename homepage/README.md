# Homepage

Deploys [Homepage](https://gethomepage.dev/) as a central
dashboard for all services.

## Environment Variables

| Variable                 | Description                                       |
|--------------------------|---------------------------------------------------|
| `HOMEPAGE_ALLOWED_HOSTS` | Allowed hostnames for access                      |
| `HOMEPAGE_VAR_*`         | Secrets for widgets (API keys, tokens), see below |

### Widget secrets

The whole `.env` is passed to the container with `env_file`, so any
`HOMEPAGE_VAR_*` variable can be used in the config files as
`{{HOMEPAGE_VAR_NAME}}`. Keep widget secrets there instead of in the YAML
files. Wrap a value in single quotes if it contains `$`
(for example `HOMEPAGE_VAR_X='ab$cd'`), so Compose does not interpolate it.
For Pi-hole v6, `key` must be an app password: v5 API tokens do not work.
For example, a Pi-hole widget in `services.yaml`:

```yaml
widget:
  type: pihole
  url: http://pihole
  version: 6
  key: "{{HOMEPAGE_VAR_PIHOLE_MAIN_KEY}}"
```

## Volumes

| Volume            | Mount         | Contents                                         | Backup |
|-------------------|---------------|--------------------------------------------------|--------|
| `homepage-config` | `/app/config` | YAML config (services, widgets, bookmarks, etc.) | Yes    |

Named Docker volume, so it can be backed up with Dokploy volume backups.
With Docker Compose the real name is prefixed with the project name
(for example `<project>_homepage-config`). Logs go to stdout
(`LOG_TARGETS=stdout`), not into the volume.

The Docker socket is not mounted: services are listed explicitly in
`services.yaml` instead of being discovered from container labels.

## Networks

Connects to `cloudflare-exposed`
(must be created by the `cloudflared` service first).
