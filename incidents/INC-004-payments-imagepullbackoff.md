# INC-004 — Payments ImagePullBackOff

## Symptom

The Payments Pod remained in:

`ImagePullBackOff`

while Redis and API workloads were able to start.

## Evidence

`kubectl describe pod` showed Kubernetes attempting to pull:

`resilience-lab-payments:local`

from:

`docker.io/library/resilience-lab-payments:local`

The registry returned:

`pull access denied`

The Azure VM Docker runtime only contained:

`resilience-lab-payments:latest`

## Investigation

The development Helm values configured Payments with:

- repository: resilience-lab-payments
- tag: local
- pullPolicy: IfNotPresent

The image existed in the Azure VM Docker runtime but not with the required tag and was not available inside the k3d nodes.

## Root Cause

The Kubernetes workload expected the local image tag, but the image was only tagged as `latest` on the host and had not been imported into the k3d cluster container runtime.

## Resolution

Created the required local tag:

`docker tag resilience-lab-payments:latest resilience-lab-payments:local`

Imported it into the k3d cluster:

`k3d image import resilience-lab-payments:local -c resilience-cluster`

## Verification

The image became visible in the k3d node container runtime.

Without manually deleting the Pod, kubelet retried and the Payments Pod recovered to:

`1/1 Running`

Final workload state:

- Redis: 1/1
- API: 2/2
- Payments: 1/1

HPA metrics also became available.

## Lessons Learned

Images available in the host Docker runtime are not automatically available to Kubernetes nodes running inside k3d.

Image repository, tag, pull policy, registry availability, and the node container runtime should all be checked when troubleshooting `ImagePullBackOff`.
