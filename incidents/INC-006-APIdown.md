# INC-006 — API Down

## Alert

- Alert: APIDown
- Severity: Critical
- Source: Prometheus

## Symptoms

Prometheus mendeteksi target API tidak tersedia dan alert APIDown masuk status FIRING.

## Investigation

### Check Deployment

```bash
kubectl get deployment resilience-lab-api -n resilience

Ditemukan:

READY: 0/0
UP-TO-DATE: 0
AVAILABLE: 0
Inspect Deployment
kubectl describe deployment resilience-lab-api -n resilience

Ditemukan:

0 desired | 0 updated | 0 total | 0 available

Event menunjukkan ReplicaSet di-scale dari 2 menjadi 0.

Root Cause

Deployment resilience-lab-api memiliki desired replica 0, sehingga tidak ada Pod API yang berjalan.

Ini menyebabkan Prometheus tidak dapat melakukan scrape target API dan memicu alert APIDown.

Recovery
kubectl scale deployment resilience-lab-api \
  -n resilience \
  --replicas=2
Verification
kubectl get pods -n resilience
kubectl get deployment resilience-lab-api -n resilience

Prometheus:

up{job="resilience-lab-api"}

Hasil:

API Pods kembali Running/Ready
Deployment kembali 2/2
Prometheus target up = 1
APIDown resolved
```
