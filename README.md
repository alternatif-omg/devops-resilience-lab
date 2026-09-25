# DevOps Resilience Lab

Hands-on DevOps/SRE lab running on an Azure VM to practice Kubernetes deployment, observability, incident troubleshooting, fault injection, and recovery.

The environment uses **Docker, k3d/K3s, Kubernetes, Helm, Prometheus, Grafana, Alertmanager, Loki, and Promtail**.

## Architecture

```text
Azure VM
│
├── Docker
│   │
│   └── k3d / K3s Cluster
│       │
│       ├── server-0
│       ├── agent-0
│       └── agent-1
│
├── Application Namespace: resilience
│   │
│   ├── API (2 replicas)
│   │      │
│   │      └── Payments
│   │
│   └── Redis
│
└── Monitoring Namespace
    │
    ├── Prometheus
    ├── Alertmanager
    ├── Grafana
    ├── Loki
    ├── Promtail
    ├── kube-state-metrics
    └── node-exporter
```

Application flow:

```text
Client
  |
  v
API
  |
  v
Payments
  |
  v
Response
```

Observability flow:

```text
Application Metrics
       |
       v
 ServiceMonitor
       |
       v
   Prometheus
      /   \
     v     v
 Grafana  Alertmanager


Container Logs
      |
      v
   Promtail
      |
      v
     Loki
      |
      v
   Grafana
```

## What I Practiced

This project was used to practice:

- Azure Linux VM administration
- Docker and Docker Compose
- Kubernetes with k3d/K3s
- Helm deployment
- Kubernetes RBAC
- NetworkPolicy
- Kubernetes Services and service discovery
- RollingUpdate and rollback
- Prometheus monitoring
- PrometheusRule and Alertmanager
- Grafana dashboards
- Loki and Promtail centralized logging
- Metrics and log correlation
- Fault injection
- Incident troubleshooting
- Recovery verification

## Incident Exercises

| Incident | Scenario                        | Main Topic              |
| -------- | ------------------------------- | ----------------------- |
| INC-001  | Docker Compose v2 compatibility | Docker                  |
| INC-002  | Headlamp RBAC                   | Kubernetes RBAC         |
| INC-003  | Helm namespace mismatch         | Helm                    |
| INC-004  | Payments ImagePullBackOff       | Container Images        |
| INC-005  | API-to-Payments blocked         | NetworkPolicy           |
| INC-006  | API Down                        | Monitoring & Alerting   |
| INC-007  | API High Error Rate             | Metrics + Logs          |
| INC-008  | High Application Latency        | Performance             |
| INC-009  | Bad Deployment                  | Rollback                |
| INC-010  | Final Validation                | End-to-End Verification |

Detailed incident reports are available in [`incidents/`](./incidents/).

## Troubleshooting Workflow

A consistent troubleshooting workflow was used throughout the lab:

```text
Alert
  |
  v
Metrics
  |
  v
Pods / Deployment
  |
  v
Events
  |
  v
Logs
  |
  v
Service / Endpoint
  |
  v
Network / Dependency
  |
  v
Root Cause
  |
  v
Recovery
  |
  v
Verification
```

Common Kubernetes commands used during investigation:

```bash
kubectl get pods -n <namespace>
kubectl describe pod <pod> -n <namespace>
kubectl logs <pod> -n <namespace>
kubectl get events -n <namespace>
kubectl get svc -n <namespace>
kubectl get endpoints -n <namespace>
kubectl rollout status deployment/<deployment> -n <namespace>
kubectl rollout undo deployment/<deployment> -n <namespace>
```

## Observability

### Metrics

Prometheus collects application and Kubernetes metrics using:

- ServiceMonitor
- kube-state-metrics
- node-exporter

Example application metrics include:

- request rate
- HTTP status codes
- request latency
- service availability

### Alerting

Example alerts exercised during the lab:

- `APIDown`
- `HighErrorRate`
- `PrometheusTargetDown`

### Logging

Promtail collects Kubernetes container logs and forwards them to Loki.

Logs can then be investigated through Grafana Explore using LogQL.

Example:

```logql
{namespace="resilience"}
```

## Example Incident: Dependency Failure

One exercise intentionally enabled failure mode on Payments.

The observed behavior was:

```text
Payments /process -> HTTP 500
          |
          v
API /pay -> HTTP 503
```

Prometheus detected the increase in HTTP 5xx while Loki was used to inspect the affected requests.

After identifying the dependency failure, the injected fault was removed and the application recovered to:

```text
POST /pay -> HTTP 201
```

## Final Validation

At the end of the lab:

```text
Redis       1/1 Ready
API         2/2 Ready
Payments    1/1 Ready
```

The observability stack was also running:

```text
Prometheus
Grafana
Alertmanager
Loki
Promtail
kube-state-metrics
node-exporter
```

Final end-to-end application test:

```text
HTTP 201 | total=0.008503s
```

## Repository Structure

```text
devops-resilience-lab/
├── README.md
├── docs/
│   ├── 01-azure-vm-setup.md
│   ├── 02-docker-environment.md
│   ├── 03-docker-compose.md
│   ├── 04-kubernetes-k3d.md
│   └── 05-headlamp.md
│
├── incidents/
│   ├── INC-001-docker-compose-v2.md
│   ├── INC-002-headlamp-rbac.md
│   ├── INC-003-helm-namespace-mismatch.md
│   ├── INC-004-payments-imagepullbackoff.md
│   ├── INC-005-api-to-payments-networkpolicy.md
│   ├── INC-006-APIdown.md
│   ├── INC-007-high-error-rate.md
│   ├── INC-008-high-latency.md
│   ├── INC-009-bad-deployment.md
│   └── INC-010-final-validation.md
│
└── notes/
    └── commands.md
```

## Key Takeaways

The main goal of this lab was not only to deploy an application, but to practice the operational workflow around it:

**deploy → observe → detect → investigate → recover → verify**

The exercises demonstrate how Kubernetes workloads, networking, monitoring, logging, and incident response interact in a cloud-hosted environment.
