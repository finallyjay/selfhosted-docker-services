# ArchiSteamFarm

Deploys [ArchiSteamFarm](https://github.com/JustArchiNET/ArchiSteamFarm)
for Steam account management and automated card farming.

## Environment Variables

| Variable                       | Description                          |
|--------------------------------|--------------------------------------|
| `ARCHISTEAMFARM_DATA`          | Local path for config, plugins, logs |
| `ARCHISTEAMFARM_LOCAL_DOMAIN`  | Domain to access the web interface   |

## Networks

Connects to `cloudflare-exposed`
(must be created by the `cloudflared` service first).
