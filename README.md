# OpenShift ArgoCD Demo

This repository contains a simple demo for deploying applications on OpenShift using ArgoCD. It demonstrates:

- ArgoCD Application for Redis Enterprise Cluster
- ArgoCD ApplicationSet for Redis Enterprise Databases
- Kustomize overlays for common and specific database configurations

## Prerequisites

- OpenShift cluster with ArgoCD operator installed
- Redis Enterprise operator installed

## Structure

- `argocd-apps/`: ArgoCD Application and ApplicationSet manifests
- `bases/`: Base Kustomize configurations for Redis cluster and databases
- `databases/`: Database-specific configurations (each subdir creates an ArgoCD app via ApplicationSet)
  - Each subdirectory contains a `kustomization.yaml` and `patch.yaml`
  - Customize `patch.yaml` with your own Redis settings (memory, persistence, eviction, etc.)

## Usage

1. Clone this repository
2. Push to GitHub under your account
3. In ArgoCD, create the Application for the cluster: `argocd-apps/redis-cluster-app.yaml`
4. Create the ApplicationSet for databases: `argocd-apps/redis-databases-appset.yaml`
5. To add a new database, create a new subdir in `databases/` with:
   - `kustomization.yaml` - references the base
   - `patch.yaml` - customize Redis settings for your needs
6. Commit and push; ArgoCD will automatically create the app and deploy the database

## Customization

Each database in `databases/` can have completely different configurations. Example customizations in `patch.yaml`:
- `memorySize`: Database memory allocation
- `persistence`: disabled | aofEverySecond | snapshotEverySecond
- `evictionPolicy`: noeviction | allkeys-lru | allkeys-random
- `replication`: true | false
- `shardCount`: Number of shards