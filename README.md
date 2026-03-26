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
3. In ArgoCD, create the Application for the cluster: `argocd-apps/redis-cluster-app.yaml`
4. Create the ApplicationSet for databases: `argocd-apps/redis-databases-appset.yaml`
5. To add a new database, create a new subdir in `databases/` with:
   - `kustomization.yaml` - references an overlay (cache, persistent, or create your own)
   - `patch.yaml` - customize specific settings (name, namespace, cluster reference, etc.)
6. Commit and push; ArgoCD will automatically create the app and deploy the database

## Structure

- `argocd-apps/`: ArgoCD Application and ApplicationSet manifests
- `bases/`: Base Kustomize configurations for Redis cluster and databases
- `overlays/`: Kustomize overlays for database types (cache, persistent) with common configurations
- `databases/`: Database-specific configurations (each subdir creates an ArgoCD app via ApplicationSet)
  - Each subdirectory contains a `kustomization.yaml` and `patch.yaml`
  - Extend an overlay and customize `patch.yaml` with specific database settings

## Usage

1. Clone this repository
2. Push to GitHub under your account
3. **Install Redis Enterprise Operator first** (see Installation Steps above)
4. In ArgoCD, create the Application for the cluster: `argocd-apps/redis-cluster-app.yaml`
5. Create the ApplicationSet for databases: `argocd-apps/redis-databases-appset.yaml`
6. To add a new database, create a new subdir in `databases/` with:
   - `kustomization.yaml` - references an overlay (cache, persistent, or create your own)
   - `patch.yaml` - customize specific settings (name, namespace, cluster reference, etc.)
7. Commit and push; ArgoCD will automatically create the app and deploy the database

## Overlays

Pre-configured database profiles:
- **cache**: Optimized for temporary data - 1GB memory, LRU eviction, no persistence
- **persistent**: Optimized for critical data - 4GB memory, no eviction, AOF persistence, replication enabled

Create additional overlays for your specific use cases (e.g., `overlays/session/`, `overlays/queue/`)