# Homepage

Deploys [Homepage](https://gethomepage.dev/) as a central
dashboard for all services. Discovers containers automatically
via Docker socket labels.

## Environment Variables

| Variable                 | Description                       |
|--------------------------|-----------------------------------|
| `HOMEPAGE_DATA`          | Local path for configuration files|
| `HOMEPAGE_ALLOWED_HOSTS` | Allowed hostnames for access      |

## Networks

Connects to `cloudflare-exposed`
(must be created by the `cloudflared` service first).
