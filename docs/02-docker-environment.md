# Docker Environment

## Objective

Prepare the Azure VM for running containerized applications.

## Components

The environment uses:

- Docker Engine
- Docker CLI
- containerd
- Docker Buildx
- Docker Compose v2

The Linux user was added to the Docker group:

```bash
sudo usermod -aG docker $USER
newgrp docker
```

## Verification

```bash
docker ps
docker run --rm hello-world
docker compose version
```

## Resource Inspection

Useful commands:

```bash
docker stats --no-stream
docker system df
```

## What I Learned

- Docker Engine and Docker CLI roles
- Container lifecycle basics
- Docker Compose v2
- Docker daemon permissions
- Container resource inspection
