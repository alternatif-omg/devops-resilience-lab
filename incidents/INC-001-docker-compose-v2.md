# INC-001 — Docker Compose v2 Compatibility

## Status

Resolved

## Summary

The development environment failed to start because the project Makefile used the legacy `docker-compose` command while the Azure VM had Docker Compose v2 installed.

## Symptom

Running:

```bash
make dev
```

returned:

```text
docker-compose up -d
make: docker-compose: No such file or directory
make: *** [Makefile:107: dev] Error 127
```

## Evidence

Docker itself was working.

Docker Compose was also available through:

```bash
docker compose version
```

The legacy command was checked separately and was unavailable.

The Makefile was inspected and found to contain:

```text
docker-compose
```

## Investigation

The failure initially appeared to be related to Docker.

However, Docker Engine was running correctly and Docker Compose v2 was installed.

The actual problem was therefore narrowed down to the automation layer: the Makefile expected a different Compose command.

## Root Cause

The project Makefile expected the legacy Docker Compose v1 command:

```text
docker-compose
```

while the environment used Docker Compose v2:

```text
docker compose
```

## Resolution

A dedicated Git branch was created:

```bash
git checkout -b fix/docker-compose-v2
```

The Makefile references were changed from:

```text
docker-compose
```

to:

```text
docker compose
```

The changes were reviewed using:

```bash
git diff
```

## Verification

The environment was started again:

```bash
make dev
```

Container status was verified:

```bash
docker compose ps
```

Application health endpoints were also tested:

```bash
curl -i http://localhost:8000/healthz
curl -i http://localhost:8001/healthz
```

The application stack started successfully.

## Troubleshooting Flow

```text
make dev failed
      |
      v
docker-compose not found
      |
      v
Check Docker installation
      |
      v
Docker is working
      |
      v
Check Docker Compose
      |
      v
docker compose is available
      |
      v
Inspect Makefile
      |
      v
Legacy command discovered
      |
      v
Update Makefile
      |
      v
Retest
      |
      v
Resolved
```

## Lessons Learned

- Start troubleshooting from the actual error message.
- Verify installed tooling before reinstalling software.
- Automation wrappers such as Makefiles can be the source of deployment failures.
- Use a separate Git branch when modifying project configuration.
- A fix should be followed by both infrastructure and application-level verification.
