# Infrastructure Directory

This directory contains all Docker and infrastructure-related files.

## Structure

```
infrastructure/
├── Dockerfile            # Production Dockerfile
└── docker-compose.yml    # Production compose configuration
```

## Usage

### Initial Setup

Run `initial-setup.sh` (located outside the app folder) to set up the production environment for the first time.

### Running the Application

From the **project root**:

```bash
docker compose -f infrastructure/docker-compose.yml up
```

### Deployment

Run `deploy.sh` (located outside the app folder) to deploy updates:

```bash
~/deploy.sh
```

### Helper Scripts

Helper scripts are created in the app root by `initial-setup.sh`:

```bash
# From app directory
./logs.sh       # View logs
./status.sh     # Check status
./restart.sh    # Restart containers
./stop.sh       # Stop containers
```

## Notes

- Docker Compose references the **project root** as the build context
- `deploy.sh` and `initial-setup.sh` are kept outside the app folder for easy access
- Environment variables should be configured in `.env` at the project root
