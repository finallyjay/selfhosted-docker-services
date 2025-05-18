# Self-Hosted Docker Services

This repository contains multiple Docker-based services, each organized into its own directory with its respective configuration and environment files.

## Structure

Each service is located in its own subdirectory with its respective `docker-compose.yml` and example environment files (`.env.example`).

Currently included services:

- ArchiSteamFarm
- (Other services you add here)

## How to Use

1. **Choose the service directory** you want to deploy.

2. **Configure environment variables:**
   - Use `.env.example` to add environment variables in Dokploy service

3. **Deploy with Dokploy:**
   - Point Dokploy to the service directory.
   - Dokploy will use the `docker-compose.yml` found there to manage the service lifecycle.

4. **Networking:**
   - Some services require external Docker networks.
   - Make sure to create these networks before deployment.

## Best Practices

- One repository with subdirectories per service allows centralized management.
- In the future, services can be split into separate repositories if needed, with this repo serving as an index.

## Contributions

Feel free to open issues or pull requests to improve configurations or add new services.

---

If you have any questions or need help setting up a service, don't hesitate to ask!
