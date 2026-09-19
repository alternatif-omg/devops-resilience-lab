# Command Reference

Commands used during the DevOps Resilience Lab.

## VM

```bash
nproc
free -h
df -h /
```

## Git

```bash
git status
git branch
git remote -v
git diff
git log --oneline
```

## Docker

```bash
docker ps
docker compose ps
docker stats --no-stream
docker system df
docker compose logs --tail=50
```

## Kubernetes

```bash
kubectl get nodes
kubectl get nodes -o wide
kubectl get namespaces
kubectl get pods -A
kubectl get svc -A
kubectl get events -A
kubectl top nodes
kubectl top pods -A
```

## Kubernetes Troubleshooting

```bash
kubectl describe pod <pod-name> -n <namespace>
kubectl logs <pod-name> -n <namespace>
kubectl get events -n <namespace>
kubectl auth can-i <verb> <resource>
```

## k3d

```bash
k3d cluster list
kubectl cluster-info
```

## Helm

```bash
helm list -A
helm dependency list deploy/helm
helm template <release> <chart>
```

## Security Reminder

Never commit:

- SSH private keys
- Kubernetes bearer tokens
- passwords
- API keys
- cloud credentials
- secrets
