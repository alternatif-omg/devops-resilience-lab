# INC-010 — Final Resilience Lab Validation



## Summary



Final validation dilakukan setelah seluruh incident exercise selesai untuk memastikan application workload, observability stack, dan end-to-end application flow kembali dalam kondisi sehat.



## Objective



Memastikan:



- application workload healthy;

- tidak ada controlled fault yang masih aktif;

- API dapat berkomunikasi dengan Payments;

- observability stack berjalan;

- end-to-end transaction berhasil;

- environment siap dianggap sebagai kondisi normal.



## Application Validation



Deployment:



```text

NAME                      READY

redis                     1/1

resilience-lab-api        2/2

resilience-lab-payments   1/1

```



Seluruh application workload berada dalam kondisi Ready.



## Service Validation



Service yang tersedia:



```text

redis                     6379/TCP

resilience-lab-api        8000/TCP

resilience-lab-payments   8001/TCP

```



API dan Payments menggunakan internal Kubernetes service untuk komunikasi antar-service.



## Observability Validation



Komponen monitoring yang berhasil diverifikasi:



```text

Alertmanager       Running

Grafana            Running

Loki               Running

Promtail           Running

Prometheus         Running

Kube State Metrics Running

Node Exporter      Running

```



Promtail berjalan pada seluruh node k3d.



Node Exporter juga berjalan pada seluruh node untuk menyediakan infrastructure metrics.



## Functional Validation



Final request dilakukan melalui API:



```text

POST /pay

```



Hasil:



```text

HTTP 201 | total=0.008503s

```



Hal ini membuktikan application flow:



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

Successful Response

```



berfungsi kembali setelah seluruh recovery dilakukan.



## Incident Exercises Completed



| Incident | Scenario |

|---|---|

| INC-001 | Docker Compose v2 compatibility |

| INC-002 | Headlamp RBAC |

| INC-003 | Helm namespace mismatch |

| INC-004 | Payments ImagePullBackOff |

| INC-005 | API-to-Payments NetworkPolicy |

| INC-006 | API Down |

| INC-007 | API High Error Rate |

| INC-008 | High Application Latency |

| INC-009 | Bad Deployment and Rollback |

| INC-010 | Final Environment Validation |



## Technologies Practiced



- Microsoft Azure VM

- Linux

- Docker

- Docker Compose

- Kubernetes

- K3s / k3d

- Helm

- Kubernetes RBAC

- Kubernetes NetworkPolicy

- Deployment and Rollback

- Prometheus

- Prometheus Operator

- PrometheusRule

- Alertmanager

- Grafana

- Loki

- Promtail

- Fault Injection

- Metrics Analysis

- Log Analysis

- Incident Troubleshooting



## Troubleshooting Workflow



Workflow yang digunakan selama lab:



```text

Alert

  |

  v

Check Metrics

  |

  v

Check Pods / Deployment

  |

  v

Check Events

  |

  v

Check Logs

  |

  v

Check Service / Endpoint

  |

  v

Check Network / Dependency

  |

  v

Identify Root Cause

  |

  v

Recovery

  |

  v

Verification

```



## Final Result



Seluruh workload utama kembali healthy.



Final application transaction berhasil dengan:



```text

HTTP 201 | total=0.008503s

```



Observability stack juga berjalan dengan Prometheus, Grafana, Alertmanager, Loki, dan Promtail.



Lab telah mencakup deployment, observability, troubleshooting, controlled fault injection, dependency failure, latency degradation, Kubernetes networking, rollback, dan recovery verification.
