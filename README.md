# DevOps Resilience Lab

Hands-on DevOps/SRE lab built on an Azure Linux VM to practice containerization, Kubernetes operations, troubleshooting, observability, and resilience testing.

The application workload is based on the `lotoos0/resilience-lab` project. This repository documents my own infrastructure setup, experiments, troubleshooting process, and lessons learned.

## Lab Architecture

```text
Windows Workstation
        |
       SSH
        |
        v
Azure Ubuntu VM
        |
      Docker
        |
       k3d
        |
   Kubernetes / K3s
   /       |       \
server-0 agent-0 agent-1
```

## Current Progress

- [x] Azure VM provisioning
- [x] Linux environment preparation
- [x] Docker Engine and Docker Compose v2
- [x] Run application using Docker Compose
- [x] Troubleshoot Docker Compose compatibility
- [x] Install kubectl, Helm, and k3d
- [x] Create three-node k3d Kubernetes cluster
- [x] Install Headlamp
- [x] Troubleshoot Kubernetes RBAC
- [x] Inspect Helm chart before deployment
- [ ] Deploy application using Helm
- [ ] Configure Prometheus and Grafana
- [ ] Configure Loki logging
- [ ] Perform resilience testing
- [ ] Perform chaos experiments
- [ ] Practice incident response and RCA

## Current Kubernetes Environment

```text
resilience-cluster
├── server-0    Control Plane
├── agent-0     Worker
└── agent-1     Worker
```

All three nodes are currently in `Ready` state.

## Troubleshooting Experience

Two issues have been investigated so far:

1. Docker Compose v2 compatibility issue in the project Makefile.
2. Headlamp RBAC authorization issue caused by an incorrect ServiceAccount binding.

Detailed investigations are documented under `incidents/`.

## Documentation

Technical notes are stored under `docs/`.

Topics currently covered:

- Azure VM setup
- Docker environment
- Docker Compose
- Kubernetes with k3d
- Headlamp

## Security

This repository intentionally excludes:

- SSH private keys
- Kubernetes bearer tokens
- passwords
- API keys
- cloud credentials
- other secrets

## Next Step

The next stage is deploying the resilience-lab application into Kubernetes using Helm, followed by observability and resilience experiments.
