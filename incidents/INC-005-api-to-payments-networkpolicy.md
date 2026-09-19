# INC-005 — API Unable to Connect to Payments Service

## Symptom

The Kubernetes workloads were healthy and all Pods were in the `Running`
state, but the API payment endpoint returned HTTP 503.

```text
STATUS: 503
BODY: {"detail":"Payments service error: All connection attempts failed"}
