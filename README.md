# Self-Hosted Docker Services

This repository contains multiple Docker-based services, each organized into its own directory with its respective configuration and environment files.

## 📁 Repository Structure

Each subdirectory represents a self-contained service and includes:

- `docker-compose.yml`: The service definition.
- `.env.example`: Example environment variables.
- `README.md`: (Optional) Service-specific documentation.

```
.
├── service1/
│   ├── docker-compose.yml
│   └── .env.example
├── another-service/
│   └── ...
```

## 🚀 Deployment via Dokploy

Use [Dokploy](https://dokploy.com) to manage service deployments in a homelab server / cloud VPS.

### GitHub Integration

Dokploy is integrated directly with GitHub. It listens for `push` events on the `main` branch and uses **watch paths** to trigger deploys **only when relevant files change**.

### ✅ Deployment Flow

1. **Push to `main`** from GitHub.
2. Dokploy checks the service's configured **watch path** (e.g. `service1/**`).
3. If the changed files match the path, the service is automatically deployed.
4. Each service has its own deployment config and isolated runtime environment.

### 💡 Example Watch Path

To deploy `service1` only when its files change:

```
Watch path: service1/**
Trigger: on push (branch: main)
```

Feel free to contribute improvements or add new services.
