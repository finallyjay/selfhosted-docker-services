# Home Assistant

Home automation hub. Runs in **bridge** networking (not `network_mode: host`)
so it coexists with the other services on this host; its UI/API port is
published to the LAN instead.

> **Trade-off of bridge mode:** on-LAN auto-discovery that relies on mDNS/DHCP
> broadcasts (e.g. some Cast/HomeKit/DHCP-based integrations) won't work
> automatically. Integrations that connect out (MQTT, Tuya cloud, most
> cloud/IP integrations) are unaffected. The robot vacuum is integrated over
> MQTT, so this is fine for that use case.

## Environment variables

| Variable | Description |
| --- | --- |
| `HOMEASSISTANT_PORT` | Host port mapped to the container's `8123`. |
| `TZ` | Timezone (e.g. `Europe/Madrid`). |
| `HOMEASSISTANT_LOCAL_DOMAIN` | Local domain used for the Homepage tile link. |

Config (including secrets, integrations and automations) lives inside the
`homeassistant-config` volume, not in env vars — Home Assistant manages it.

## Networks

Bridge only. No network is declared: Dokploy attaches `dokploy-network` on
deploy once the service has a domain, and Traefik routes from there.

## First run

1. Deploy. Home Assistant generates its config in the `homeassistant-config`
   volume on first boot.
2. Open `http://<HOST_LAN_IP>:<HOMEASSISTANT_PORT>` **on the LAN** and complete
   onboarding (create the owner account). Do the initial setup locally, not
   through the tunnel.
3. Enable **two-factor authentication** for every account
   (Profile → Security). This is the main line of defence for remote access.

## Remote access (mobile app, no VPN)

The goal is for the companion app to work for the user **and** a non-technical
family member, without VPN/Tailscale.

1. In **Dokploy**, add a domain to this service (e.g. `home.<yourdomain>`).
   Dokploy puts the container on `dokploy-network` and generates the Traefik
   route.
2. In **Cloudflare**, add that hostname to the Tunnel (`cloudflared/`) pointing
   to `http://dokploy-traefik`.
3. **Do NOT add a Cloudflare Access policy to this hostname.** Cloudflare
   Access breaks the Home Assistant companion app login. Protect the endpoint
   with Home Assistant's own auth + 2FA, and optionally a Cloudflare **WAF**
   rule (rate-limiting / geo-block) on the hostname.

### Required: trust the reverse proxy

Home Assistant rejects requests coming through a reverse proxy unless told to
trust it. After the first boot, edit `configuration.yaml` in the
`homeassistant-config` volume and add:

```yaml
http:
  use_x_forwarded_for: true
  trusted_proxies:
    - 172.16.0.0/12   # Docker / dokploy-network (Traefik → HA)
    - 127.0.0.1
```

Restart Home Assistant afterwards. Without this, access through the tunnel
returns `400: Bad Request`.

## Connecting the robot vacuum (Congatudo over MQTT)

Home Assistant has no built-in MQTT broker — the `mosquitto/` service provides
it. Once Mosquitto is up:

1. Settings → Devices & Services → Add Integration → **MQTT**.
2. Broker: the **host's LAN IP**, port `MOSQUITTO_PORT` (default `1883`, the
   published Mosquitto port). Use the host IP rather than a container name — HA
   and Mosquitto don't share a user-defined Docker network.
3. Username / password: the Mosquitto credentials (`MOSQUITTO_USERNAME` /
   `MOSQUITTO_PASSWORD`).

Congatudo on the robot publishes to the same broker; its entities then appear
in Home Assistant via MQTT discovery.

## Notes

- `security_opt: no-new-privileges:true` is set. If you later add an
  integration that needs USB/Bluetooth passthrough, you'll need to relax this
  and add the relevant `devices:`.
- Watchtower auto-updates the `:stable` image (label opt-in). Home Assistant
  ships breaking changes on a schedule; review release notes if an update
  misbehaves.
