# INC-003 — Helm Namespace Mismatch

## Symptom

Initial Helm installation failed with:

`namespaces "resilience-lab" not found`

The release was installed using namespace `resilience`, but multiple rendered resources referenced `resilience-lab`.

## Evidence

The installation command used:

`--namespace resilience --create-namespace`

Inspection of the rendered Helm manifests showed multiple resources containing:

`namespace: resilience-lab`

while some other resources correctly rendered:

`namespace: resilience`

## Investigation

The Helm templates were searched for hardcoded namespace declarations.

Multiple templates including NetworkPolicy, ResourceQuota, LimitRange, PDB, Redis, and HPA resources contained a hardcoded `resilience-lab` namespace.

## Root Cause

Several Helm templates hardcoded the namespace instead of using the namespace selected for the Helm release.

## Resolution

Changed:

`namespace: resilience-lab`

to:

`namespace: {{ .Release.Namespace }}`

The chart was rendered and linted again before deployment.

The failed Helm release was then upgraded using the corrected chart.

## Verification

Helm reported:

- Revision: 2
- Status: deployed
- Namespace: resilience

The Kubernetes resources were successfully created in the intended namespace.

## Lessons Learned

A Helm release namespace and a hardcoded manifest namespace can conflict.

Using `.Release.Namespace` makes the chart portable across namespaces and allows `helm template` and `helm lint` to detect configuration problems before deployment.
