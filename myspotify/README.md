# MySpotify

Deploys [Your Spotify](https://github.com/Yooooomi/your_spotify)
to track and visualize personal Spotify listening statistics.
Includes three containers: backend API, MongoDB database,
and frontend client.

## Environment Variables

| Variable                   | Description                       |
|----------------------------|-----------------------------------|
| `MYSPOTIFY_DATA`           | Local path for database storage   |
| `MYSPOTIFY_DOMAIN`         | Domain to access the web interface|
| `MYSPOTIFY_BACKEND_DOMAIN` | Domain for the backend API        |
| `MYSPOTIFY_APP_PUBLIC`     | Spotify application public key    |
| `MYSPOTIFY_APP_SECRET`     | Spotify application secret key    |

## Networks

Connects to `cloudflare-exposed`
(must be created by the `cloudflared` service first).
