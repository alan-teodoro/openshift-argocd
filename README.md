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
- `overlays/`: Kustomize overlays for database types (cache, persistent)
- `databases/`: Database-specific configurations (each subdir creates an ArgoCD app via ApplicationSet)

## Usage

1. Clone this repository
2. Push to GitHub under your account
3. In ArgoCD, create the Application for the cluster: `argocd-apps/redis-cluster-app.yaml`
4. Create the ApplicationSet for databases: `argocd-apps/redis-databases-appset.yaml`
5. To add a new database, create a new subdir in `databases/` with `kustomization.yaml` and `patch.yaml` (see examples)
6. Commit and push; ArgoCD will automatically create the app and deploy the database

## Demo Flow

- Deploy the cluster app
- Show the ApplicationSet watching `databases/`
- Add a new database by creating a subdir and files
- Demonstrate how overlays apply common configs