# 🚀 Memos & PostgreSQL 17 Deployment on k3s

This repository contains declarative Kubernetes manifest configurations for deploying **Memos**—a lightweight note-taking service—backed by a **PostgreSQL 17** database and exposed via **Traefik Ingress** on a **k3s** cluster.

## 🏗️ Architecture Overview

The application stack is deployed inside a dedicated Kubernetes Namespace (`moshiri-app`):

* **Namespace Isolation:** Multi-tenant separation using dedicated Kubernetes `Namespace`.
* **Database Layer:** Stateful **PostgreSQL 17** deployment paired with a `ClusterIP` Service on port `5432`. Configuration and credentials are decoupled via `Secret` and `ConfigMap` objects.
* **Application Layer:** **Memos** instance (`neosmemo/memos:latest`) connected to PostgreSQL using DSN environment variables via port `5230`.
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
   git clone git@github.com:YOUR_USERNAME/memos-k3s-deployment.git
   cd memos-k3s-deployment
   ```
2. **Apply manifests in sequence:**
   ```bash
   kubectl apply -f k8s/
   ```
3. **Verify Cluster State:**
Ensure all Pods, Services, and Ingress resources are healthy:
   ```bash
   kubectl get all,ingress -n moshiri-app
   ```

## 🔒 Security Best Practices

* **Credential Masking:** Sensitive strings inside `k8s/02-configmap.yaml`, `k8s/03-secret.yaml`, and `k8s/05-memos.yaml` have been sanitized with dummy placeholders (`your_secure_pass!`, `your_db_user!`) prior to source control commit.
* **Least Privilege:** Secrets are scoped strictly to the `moshiri-app` namespace to prevent cross-namespace unauthorized access.
* **Environment Separation:** Database connection details are injected at runtime via Kubernetes primitives rather than hardcoded into container image layers.

## 🛠️ Tech Stack

* **Orchestration:** Kubernetes / k3s
* **Application:** Memos (`neosmemo/memos:latest`)
* **Database:** PostgreSQL 17
* **Ingress Class:** Traefik
* **Configuration:** YAML (Declarative Manifests)

---

## 📜 License

This repository is published under the **MIT License**. Feel free to modify and adapt these manifests for your cluster deployments.