# INC-002 — Headlamp RBAC Authorization

## Status

Resolved

## Summary

Headlamp authentication succeeded, but the dashboard displayed no Kubernetes resources because the authenticated ServiceAccount did not have the expected RBAC permissions.

## Symptom

The Headlamp web interface was accessible and login succeeded.

However, cluster information such as Nodes and Pods was not displayed.

The Kubernetes cluster itself was healthy:

```bash
kubectl get nodes
```

showed all three nodes in the `Ready` state.

## Evidence

Authorization was tested directly:

```bash
kubectl auth can-i get nodes \
  --as=system:serviceaccount:headlamp:headlamp-admin
```

and:

```bash
kubectl auth can-i list pods --all-namespaces \
  --as=system:serviceaccount:headlamp:headlamp-admin
```

Both returned:

```text
no
```

This showed that authentication was working, but authorization was not.

## Investigation

The existing ClusterRoleBinding was inspected:

```bash
kubectl describe clusterrolebinding headlamp-admin
```

The role was:

```text
Role:
  Kind: ClusterRole
  Name: cluster-admin
```

but its subject was:

```text
ServiceAccount  headlamp  headlamp
```

The Headlamp login token was associated with:

```text
ServiceAccount  headlamp-admin  headlamp
```

The identity receiving the permissions was therefore different from the identity used for authentication.

## Root Cause

The ClusterRoleBinding referenced the wrong ServiceAccount.

Conceptually:

```text
cluster-admin
     |
     v
ServiceAccount: headlamp
                       X
ServiceAccount: headlamp-admin
     |
     v
Headlamp login
```

The `headlamp-admin` ServiceAccount could authenticate but was not authorized to read cluster resources.

## Resolution

The incorrect ClusterRoleBinding was replaced with one referencing:

```text
headlamp:headlamp-admin
```

For this isolated learning environment, the ServiceAccount was bound to `cluster-admin`.

A production environment should instead apply least-privilege RBAC.

## Verification

Authorization was checked again:

```bash
kubectl auth can-i get nodes \
  --as=system:serviceaccount:headlamp:headlamp-admin
```

and:

```bash
kubectl auth can-i list pods --all-namespaces \
  --as=system:serviceaccount:headlamp:headlamp-admin
```

Both returned:

```text
yes
```

Headlamp could then access Kubernetes cluster resources.

## Troubleshooting Flow

```text
Headlamp UI empty
       |
       v
Check cluster health
       |
       v
Cluster healthy
       |
       v
Check ServiceAccount permissions
       |
       v
kubectl auth can-i -> NO
       |
       v
Inspect ClusterRoleBinding
       |
       v
Wrong ServiceAccount subject
       |
       v
Correct RBAC binding
       |
       v
kubectl auth can-i -> YES
       |
       v
Resolved
```

## Lessons Learned

- Authentication and authorization are separate concepts.
- Successful login does not guarantee access to Kubernetes resources.
- `kubectl auth can-i` is useful for diagnosing RBAC issues.
- Always verify both `subjects` and `roleRef` when inspecting RoleBindings or ClusterRoleBindings.
- Dashboard problems can originate from Kubernetes permissions rather than from the dashboard application itself.
