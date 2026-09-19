# Kubernetes with k3d

## Objective

Create a Kubernetes learning environment without immediately using a managed Kubernetes service such as AKS.

## Tools

- kubectl
- k3d
- K3s
- Helm
- Docker

## Architecture

```text
Azure VM
   |
 Docker
   |
  k3d
   |
Kubernetes / K3s
   |
   +-- server-0
   |    Control Plane
   |
   +-- agent-0
   |    Worker
   |
   +-- agent-1
        Worker
```

These Kubernetes nodes run as containers inside the same Azure VM. They are not separate Azure virtual machines.

## Cluster

Cluster name:

```text
resilience-cluster
```

Verification:

```bash
kubectl get nodes
```

Observed nodes:

```text
k3d-resilience-cluster-server-0
k3d-resilience-cluster-agent-0
k3d-resilience-cluster-agent-1
```

All nodes reached the `Ready` state.

The observed K3s version was:

```text
v1.35.5+k3s1
```

## Kubernetes System Components

The cluster was inspected with:

```bash
kubectl get pods -A
```

System components included:

- CoreDNS
- Traefik
- Metrics Server
- Local Path Provisioner
- ServiceLB

## Helm Pre-deployment Inspection

Before installing the application, Helm dependencies were inspected:

```bash
helm dependency list deploy/helm
```

Local dependencies:

```text
api       0.1.0
payments  0.1.0
```

The chart was rendered without installing it:

```bash
helm template resilience-lab deploy/helm \
  -f deploy/helm/values-dev.yaml \
  > /tmp/resilience-rendered.yaml
```

The rendered manifests contained resources including:

- ConfigMap
- Deployment
- HorizontalPodAutoscaler
- LimitRange
- NetworkPolicy
- Pod
- PodDisruptionBudget
- ResourceQuota
- Service

## What I Learned

- Kubernetes cluster versus node
- Control plane versus worker node
- Kubernetes system Pods
- Namespaces
- Helm dependencies
- Pre-deployment inspection using `helm template`
