# OpenShift ArgoCD Demo

This repository contains a simple demo for deploying applications on OpenShift using ArgoCD. It demonstrates:

- ArgoCD Application for Redis Enterprise Cluster
- ArgoCD ApplicationSet for Redis Enterprise Databases
- Kustomize overlays for common and specific database configurations

## Prerequisites

- OpenShift cluster with ArgoCD operator installed
- Redis Enterprise operator installed (already installed in your cluster)

## Usage

1. Clone this repository
2. Push to GitHub under your account
3. Install the Redis Enterprise Operator
4. In ArgoCD, create the cluster Application: `argocd-apps/redis-cluster-app.yaml`
5. Create the databases ApplicationSet: `argocd-apps/redis-databases-appset.yaml`
6. Commit and push changes; ArgoCD will reconcile the cluster and database applications

## Structure

- `argocd-apps/`: ArgoCD Application and ApplicationSet manifests
- `bases/`: Base Kustomize configurations for Redis cluster and databases
- `overlays/`: Kustomize overlays for database types (cache, persistent) with common configurations
- `databases/`: Database-specific configurations (each subdir creates an ArgoCD app via ApplicationSet)
  - Each subdirectory contains `kustomization.yaml`, `patch.yaml`, and optionally `route-patch.yaml`
  - Extend an overlay and customize the patch files with specific settings

## Database Lifecycle

### Create a new database

1. Create a new directory under `databases/` by copying an existing one such as `databases/cache-dev`
2. Update `patch.yaml` with the new database name, labels, and namespace
3. Update `route-patch.yaml` with the new route name and namespace
4. Commit and push
5. The `redis-databases` ApplicationSet will generate a new ArgoCD Application for the directory

### Remove a database

1. Delete the database directory from `databases/`
2. Commit and push
3. Refresh the `redis-databases` ApplicationSet if ArgoCD does not notice the deletion quickly
4. Once the ApplicationSet reconciles, it removes the generated Application and ArgoCD prunes the RedisEnterpriseDatabase and Route

### Refresh behavior

ArgoCD does not always notice new or removed `databases/*` directories immediately. This is normal when the Git generator has not reconciled yet.

If a new database does not appear, or a removed database is still present, refresh the `redis-databases` ApplicationSet:

```bash
oc patch applicationset redis-databases -n openshift-gitops --type merge -p '{"metadata":{"annotations":{"argocd.argoproj.io/refresh":"hard"}}}'
```

If a generated Application is stuck after the cluster becomes healthy again, a manual sync can help:

```bash
oc patch application <app-name> -n openshift-gitops --type merge -p '{"operation":{"initiatedBy":{"username":"admin"},"sync":{"prune":true}}}'
```

### ArgoCD UI note

It is normal in some OpenShift GitOps installations for `ApplicationSet` not to appear as a dedicated menu in the ArgoCD UI. Depending on the console integration, you may only see the generated `Application` objects. In that case, use the OpenShift web console or `oc` to refresh the `ApplicationSet`.

## Overlays

Pre-configured database profiles:
- **cache**: Optimized for temporary data - 512MB memory, LRU eviction, no persistence, no replication
- **persistent**: Optimized for critical data - 512MB memory, no eviction, AOF persistence, no replication

Create additional overlays for your specific use cases (e.g., `overlays/session/`, `overlays/queue/`)
