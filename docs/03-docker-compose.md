# Docker Compose Deployment

## Objective

Run the resilience-lab application using Docker Compose before moving it to Kubernetes.

## Application Services

The Compose configuration was inspected using:

```bash
docker compose config --services
```

Four main services were identified:

```text
postgres
redis
payments
api
```

## Starting the Environment

The project provides a Makefile:

```bash
make dev
```

The first attempt failed because the Makefile expected the legacy `docker-compose` command while the VM used Docker Compose v2.

That troubleshooting process is documented in:

`../incidents/INC-001-docker-compose-v2.md`

After correcting the command, the environment started successfully.

## Verification

Container status:

```bash
docker compose ps
```

Application health:

```bash
curl -i http://localhost:8000/healthz
curl -i http://localhost:8001/healthz
```

Logs:

```bash
docker compose logs --tail=30 api
docker compose logs --tail=30 payments
```

## Key Lesson

A running VM or container does not automatically mean the application itself is healthy.

```text
VM Running
    |
Docker Running
    |
Container Running
    |
Application Healthy
```

Each layer should be verified independently.
