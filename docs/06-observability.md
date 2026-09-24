# Observability Stack



## Overview



The Kubernetes resilience lab uses the following observability stack:



- Prometheus — metrics collection and alerting

- Grafana — dashboards and visualization

- Loki — centralized log storage

- Promtail — Kubernetes log collection

- Alertmanager — alert handling



Architecture:



Application Pods

├── Metrics → ServiceMonitor → Prometheus → Grafana

└── Logs → Promtail → Loki → Grafana



## Prometheus



Prometheus is deployed using `kube-prometheus-stack` in the

`monitoring` namespace.



Application targets:



- resilience-lab-api

- resilience-lab-payments



Both targets were validated as `UP`.



Example verification:



```promql

up{namespace="resilience"}
