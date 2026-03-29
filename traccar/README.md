# Traccar

Deploys [Traccar](https://www.traccar.org/) GPS tracking
server. Supports multiple tracking protocols on configurable
ports.

## Environment Variables

| Variable       | Description                              |
|----------------|------------------------------------------|
| `TRACCAR_DATA` | Local path for logs, configuration, data |

## Networks

Connects to `dokploy-network` (external, managed by Dokploy).

## Notes

- `traccar.xml` is mounted as read-only — edit it on the
  host before starting the container.
- Default ports: `25002` (mapped to 5013) and
  `25003` (mapped to 5023).
