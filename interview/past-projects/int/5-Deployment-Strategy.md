# Deployment Strategy

## Infrastructure Overview

The project is deployed on **AWS EC2 instances**. Since the platform doesn't have a large number of concurrent users, the infrastructure is kept simple and cost-effective:

- **Main application** (Next.js frontend + Spring Boot backend) — single EC2 instance
- **AI rendering engine** (FlaskAPI) — separate EC2 instance

## CI/CD Pipeline — Bitbucket Pipelines

The deployment process is automated using **Bitbucket Pipelines**:

```
Developer pushes code / merges PR
        │
        ▼
┌──────────────────────────┐
│   Bitbucket Pipeline     │
│                          │
│  1. Build JAR file       │
│     (Maven/Gradle)       │
│  2. Run unit tests       │
│  3. Copy JAR to EC2      │
│     via SCP/SSH          │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│   EC2 Instance           │
│                          │
│  1. Execute deployment   │
│     script               │
│  2. Blue-green swap      │
│  3. Health check         │
│  4. Cleanup old version  │
└──────────────────────────┘
```

### Pipeline Steps

1. **Build** — compiles the Spring Boot application into a JAR file
2. **Test** — runs unit tests to ensure no regressions
3. **Transfer** — copies the JAR file to the EC2 instance via SCP over SSH
4. **Deploy** — executes deployment scripts on the EC2 instance

## Blue-Green Deployment

We use a **blue-green deployment strategy** to minimize downtime:

### How It Works

1. **Blue** is the currently running version serving live traffic
2. **Green** is the new version being deployed
3. The deployment script:
   - Starts the new version (Green) on a different port
   - Waits for the Green instance to pass health checks
   - Switches the reverse proxy (Nginx) to point to Green
   - Stops the old version (Blue)
4. If Green fails health checks, the script aborts and Blue continues serving traffic

### EC2 Scripts

The EC2 instance has multiple management scripts:

| Script | Purpose |
|--------|---------|
| `start.sh` | Starts the application (used for initial setup or recovery) |
| `stop.sh` | Gracefully stops the running application |
| `restart.sh` | Stops and starts (used for quick restarts) |
| `deploy.sh` | Full blue-green deployment (starts new version, swaps, stops old) |

## Environment Management

### Configuration

- Environment-specific properties are managed through **Spring profiles** (`application-dev.yml`, `application-prod.yml`)
- Sensitive configuration (database credentials, API keys) are stored as environment variables on the EC2 instance, not in the codebase
- The AI rendering engine URL is configured as an environment variable so it can be changed without redeploying

### Environments

| Environment | Purpose | Infrastructure |
|-------------|---------|---------------|
| Local | Development | Docker Compose (MariaDB + app) |
| Staging | Testing before production | Shared EC2 or local with prod-like config |
| Production | Live Korean market site | EC2 instance with blue-green deployment |

## AI Rendering Engine Deployment

The AI rendering engine has its own deployment process:

- Built and deployed separately from the main application
- Uses a similar script-based deployment on its dedicated EC2 instance
- FlaskAPI application managed with **Gunicorn** as the WSGI server
- Updates are less frequent than the main application since the AI engine changes less often

## Monitoring

- **Application logs** — written to files on the EC2 instance, rotated daily
- **Health checks** — simple HTTP endpoint (`/health`) checked by the deployment scripts
- **Manual monitoring** — given the smaller scale, monitoring is primarily log-based with alerts configured for critical errors

## Key Design Decisions

| Decision | Reasoning |
|----------|-----------|
| **Bitbucket Pipelines** | Team already uses Bitbucket for source control; minimal setup to add CI/CD |
| **JAR + SCP (not Docker)** | Simpler deployment for a single EC2 instance; Docker adds overhead without benefit at this scale |
| **Blue-green on single EC2** | Achieves near-zero downtime without needing multiple instances or a load balancer |
| **Separate EC2 for AI** | Isolates compute-heavy AI workload from the main app to prevent performance interference |
| **Script-based deployment** | Simple, transparent, and easy to debug; orchestration tools are overkill for this project size |

## Future Considerations

- **Containerization** — if the project scales or the international version launches, migrating to Docker + ECS would improve deployment consistency
- **CDN** — adding CloudFront for static assets would improve load times for Korean users
- **Auto-scaling** — if traffic grows significantly, moving to an auto-scaling group behind a load balancer would be the next step
