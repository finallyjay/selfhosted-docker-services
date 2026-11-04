# Homepage

Deploys [Homepage](https://gethomepage.dev/) as a central
dashboard for all services.

## Environment Variables

| Variable                 | Description                                       |
|--------------------------|---------------------------------------------------|
| `HOMEPAGE_DATA`          | Local path for configuration files                |
| `HOMEPAGE_ALLOWED_HOSTS` | Allowed hostnames for access                      |
| `HOMEPAGE_VAR_*`         | Secrets for widgets (API keys, tokens), see below |

### Widget secrets

The whole `.env` is passed to the container with `env_file`, so any
`HOMEPAGE_VAR_*` variable can be used in the config files as
`{{HOMEPAGE_VAR_NAME}}`. Keep API keys there instead of in the YAML files.
For example, a Pi-hole widget in `services.yaml`:

```yaml
widget:
  type: pihole
  url: http://pihole
  version: 6
  key: "{{HOMEPAGE_VAR_PIHOLE_MAIN_KEY}}"
```

## Networks

Connects to `cloudflare-exposed`
(must be created by the `cloudflared` service first).
