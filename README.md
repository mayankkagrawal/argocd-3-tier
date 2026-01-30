# ArgoCD 3-Tier Application – GitOps Implementation

## Prerequisites

- Kubernetes cluster
- ArgoCD installed in the cluster
- Git repository accessible by ArgoCD
- kubectl access (only for initial bootstrap)

---

## Overview

This repository deploys a 3-tier application using ArgoCD and GitOps principles.

Components:
- Database (PostgreSQL)
- Database schema migration (Kubernetes Job)
- Backend service
- Frontend service

The deployment uses:
- App of Apps pattern
- Sync Waves for ordered deployment
- Automated sync with self-healing and pruning

---

## Repository Structure

argocd-3-tier/
├── apps/
│ ├── root-app.yaml
│ ├── database-app.yaml
│ ├── backend-app.yaml
│ └── frontend-app.yaml
│
├── database/
│ ├── namespace.yaml
│ ├── secret.yaml
│ ├── deployment.yaml
│ └── migration-job.yaml
│
├── backend/
│ ├── deployment.yaml
│ └── service.yaml
| └── namespace.yaml
│
├── frontend/
│ ├── deployment.yaml
│ └── service.yaml
|  └── namespace.yaml
│
└── README.md

---

## Deployment Flow

1. Root application bootstraps all child applications
2. Database resources are deployed
3. Database migration job runs after database is ready
4. Backend is deployed
5. Frontend is deployed

---

## Sync Wave Configuration

### Resource-level (Database Application)

| Resource | Sync Wave |
|--------|-----------|
| Namespace, Secret, Deployment, Service | 0 |
| Migration Job | 1 |

### Application-level

| Application | Sync Wave |
|------------|-----------|
| Database | 0 |
| Backend | 2 |
| Frontend | 3 |

---

## Initial Bootstrap

This is a one-time step.

```bash
kubectl apply -f apps/root-app.yaml

After this, all deployments are managed by ArgoCD via Git.