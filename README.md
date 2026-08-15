# 🚀 Memos & PostgreSQL 17 Deployment on k3s

This repository contains declarative Kubernetes manifest configurations for deploying **Memos**—a lightweight note-taking service—backed by a **PostgreSQL 17** database and exposed via **Traefik Ingress** on a **k3s** cluster.

## 🏗️ Architecture Overview

The application stack is deployed inside a dedicated Kubernetes Namespace (`moshiri-app`):

* **Namespace Isolation:** Multi-tenant separation using dedicated Kubernetes `Namespace`.
* **Database Layer:** Stateful **PostgreSQL 17** deployment paired with a `ClusterIP` Service on port `5432`. Configuration and credentials are decoupled via `Secret` and `ConfigMap` objects.
* **Application Layer:** **Memos** instance (`neosmemo/memos:latest`) connected to PostgreSQL using DSN environment variables via port `5230`.
* **Resource Optimization:** Explicit CPU and Memory `requests` and `limits` configured across all workloads to ensure cluster stability and prevent resource starvation.
* **Ingress Routing:** **Traefik Ingress Controller** managing external HTTP traffic for `moshiri.osdl.ir` down to the Memos service.

## 📁 Repository Structure

```text
.
├── k8s/
│   ├── 01-namespace.yaml   # Target Namespace definition
│   ├── 02-configmap.yaml   # Non-sensitive database environment variables
│   ├── 03-secret.yaml      # Sensitive database password (base64/stringData)
│   ├── 04-postgres.yaml    # PostgreSQL Deployment and ClusterIP Service
│   ├── 05-memos.yaml       # Memos App Deployment and ClusterIP Service
│   └── 06-ingress.yaml     # Traefik Ingress rule for domain routing
└── README.md               # Documentation
```

## 🚀 Quick Start & Deployment Guide

### Prerequisites
* A running **k3s** or vanilla **Kubernetes** cluster.
* `kubectl` CLI configured with cluster admin context.
* An active Ingress controller (e.g., Traefik, NGINX).

### Step-by-Step Installation

1. **Clone the repository:**
   ```bash
   git clone git@github.com:IliaMoshiri/memos-k3s-deployment.git
   cd memos-k3s-deployment
   ```
2. **Configure Secrets:**
   Update `k8s/03-secret.yaml` with your actual database credentials or create the secret manually before deploying:
   ```
   POSTGRES_PASSWORD: "<YOUR_DATABASE_PASSWORD>"
   MEMOS_DSN: "postgresql://memos_user:<YOUR_DATABASE_PASSWORD>@postgres-service:5432/memos_db?sslmode=disable"
   ```
3. **Apply manifests in sequence:**
   ```bash
   kubectl apply -f k8s/
   ```
4. **Verify Cluster State:**
Ensure all Pods, Services, and Ingress resources are healthy:
   ```bash
   kubectl get all,ingress -n moshiri-app
   ```

## 🔒 Security Best Practices

* **Zero Hardcoded Secrets:** Sensitive strings (both database password and full connection DSNs) are decoupling-managed via Kubernetes `Secret` resources using `secretKeyRef`.
* **Credential Masking:** All secrets in source control are sanitized using standard placeholders (`<YOUR_DATABASE_PASSWORD>`) to prevent secret leakage.
* **Resource Guardrails** Enforced CPU and Memory resource constraints (`requests` & `limits`) on container definitions prevent noisy neighbor issues.
* **Least Privilege Scope:** Resources are strictly scoped to the `moshiri-app` namespace.

## 🛠️ Tech Stack

* **Orchestration:** Kubernetes / k3s
* **Application:** Memos (`neosmemo/memos:latest`)
* **Database:** PostgreSQL 17
* **Ingress Class:** Traefik
* **Configuration:** YAML (Declarative Manifests)

---

## 📜 License

This repository is published under the **MIT License**. Feel free to modify and adapt these manifests for your cluster deployments.