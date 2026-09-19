# Headlamp Kubernetes Dashboard

## Objective

Add a graphical interface for inspecting the Kubernetes cluster while continuing to use kubectl for administration and troubleshooting.

## Deployment

Headlamp was installed into the Kubernetes cluster using Helm and placed in its own namespace.

## Access Architecture

The dashboard is not intentionally exposed directly to the public internet.

```text
Windows Browser
      |
localhost
      |
SSH Tunnel
      |
Azure VM
      |
kubectl port-forward
      |
Headlamp Service
      |
Kubernetes API
```

## Authentication and RBAC

A ServiceAccount named:

```text
headlamp-admin
```

was used for the lab.

A ClusterRoleBinding connects this ServiceAccount with Kubernetes permissions.

For this isolated learning environment, `cluster-admin` was used. Production environments should normally follow least-privilege RBAC principles.

## Troubleshooting

Headlamp initially allowed authentication but displayed no Kubernetes resources.

Permission checks returned:

```text
no
```

The ClusterRoleBinding was inspected and found to reference a different ServiceAccount.

After correcting the binding, authorization checks returned:

```text
yes
```

The complete troubleshooting process is documented in:

`../incidents/INC-002-headlamp-rbac.md`

## What I Learned

- Kubernetes ServiceAccounts
- ClusterRole and ClusterRoleBinding
- Authentication versus authorization
- `kubectl auth can-i`
- SSH tunneling
- Secure dashboard access
