# Contributing

Thanks for your interest in contributing!

## Adding a New Service

Every service must follow this structure:

```text
service-name/
├── docker-compose.yml
├── .env.example
└── README.md
```

### docker-compose.yml

Follow the standard property order:

`image` > `container_name` > `hostname` > `restart` >
`command` > `depends_on` > `devices`/`cap_add` >
`security_opt` > `dns`/`dns_search` > `healthcheck` >
`networks` > `ports` > `volumes` > `environment` >
`logging` > `labels`

Every service must include:

- `restart: unless-stopped`
- `security_opt: no-new-privileges:true`
- `logging` with json-file driver (10m max, 3 files)
- Watchtower label: `com.centurylinklabs.watchtower.enable=true`
- Homepage labels if the service has a web UI

### .env.example

- List all required variables with placeholder values.
- Use `SERVICE_NAME_VARIABLE` naming convention.
- Data paths should use the `*_DATA` suffix.
- Never include real credentials.

### README.md

- Link to the upstream project.
- Include an environment variables table.
- Document network requirements.
- Keep lines under 80 characters for lint compliance.

## Commit Messages

Follow the format: `[ServiceName] type: Description`

Examples:

- `[Pihole] feat: Add healthcheck configuration`
- `[Plex] fix: Remove privileged mode`

## Pull Requests

- One service per PR when possible.
- Include a test plan describing how to verify the changes.
