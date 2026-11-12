# Mosquitto (MQTT broker)

Eclipse Mosquitto broker. Home Assistant ships no broker of its own, so this
service provides the MQTT bus that Home Assistant and the robot vacuum
(Congatudo) talk over.

Port `1883` is published to the LAN so both the robot and Home Assistant can
reach it at `<HOST_LAN_IP>:1883`.

## Environment variables

| Variable | Description |
| --- | --- |
| `MOSQUITTO_PORT` | Host port mapped to the container's `1883`. |
| `MOSQUITTO_USERNAME` | MQTT username (shared by Home Assistant and the robot). |
| `MOSQUITTO_PASSWORD` | MQTT password. **Required** — deploy fails if empty. |
| `TZ` | Timezone (e.g. `Europe/Madrid`). |

## Config generation (no committed config file)

This repo enforces exactly three files per service
(`docker-compose.yml`, `.env.example`, `README.md`), so `mosquitto.conf` and the
password file are **not** committed. Instead the container's `command`
regenerates both on every start from the environment:

- `mosquitto_passwd` writes a hashed `passwd` file from `MOSQUITTO_USERNAME` /
  `MOSQUITTO_PASSWORD`.
- A minimal `mosquitto.conf` is written enabling a `1883` listener,
  `allow_anonymous false`, the password file, and persistence under
  `/mosquitto/data/`.

This is idempotent: changing the credentials in Dokploy and redeploying
re-creates the password file.

## Networks

Bridge only. No network is declared. Nothing on `dokploy-network` needs to
reach the broker — Home Assistant connects over the LAN
(`<HOST_LAN_IP>:1883`), which avoids depending on Dokploy attaching the two
containers to the same user-defined network.

## Security

- Anonymous access is disabled; a username and password are always required.
- Port `1883` is plaintext MQTT exposed on the LAN. That's acceptable for a
  trusted home LAN with authentication, but do **not** forward this port to the
  internet. Remote access goes through Home Assistant, not the broker.

## Wiring it up

- **Home Assistant:** add the MQTT integration, broker `<HOST_LAN_IP>`, port
  `1883`, with these credentials. See `homeassistant/README.md`.
- **Robot (Congatudo):** point its MQTT config at `<HOST_LAN_IP>:1883` with the
  same credentials. Topic/identifier details live in
  `~/projects/homeassistant/cecotec-conga-3290/CONGA-3290.md`.
