# GitOps Kubernetes Manifests

> **Single Source of Truth (SSOT)** for Kubernetes deployment manifests managed through a centralized **GitOps** workflow with **Argo CD**.

---

# 🎯 Overview

This repository serves as the centralized GitOps repository for multiple independent applications deployed on a Kubernetes cluster.

It acts as the **Single Source of Truth (SSOT)** for the desired cluster state by storing Kubernetes deployment manifests in Git.

Rather than allowing CI pipelines to deploy directly to Kubernetes, each application's CI pipeline updates only its own deployment manifest. **Argo CD** continuously monitors this repository and automatically synchronizes the cluster with the desired state stored in Git.

This centralized approach allows multiple independent applications, built using different CI platforms, to share a unified Continuous Deployment workflow.

---

# 🏗 Platform Architecture

```text
                           Developers
                                │
             ┌──────────────────┴──────────────────┐
             │                                     │
             ▼                                     ▼
     Spring Boot Application            Bootstrap Application
             │                                     │
             ▼                                     ▼
        Jenkins CI                         GitLab CI/CD
             │                                     │
             └───────────────┬─────────────────────┘
                             │
                             ▼
             Update Kubernetes Manifests
                 (Image Tag Only)
                             │
                             ▼
          GitOps Manifests Repository (SSOT)
                             │
                             ▼
                         Argo CD
                             │
                             ▼
                    Kubernetes Cluster
```

---

# 🚀 GitOps Workflow

## 1. Continuous Integration (CI)

Each application is built independently using its own CI platform while sharing the same centralized GitOps deployment repository.

### Spring Boot Application (Jenkins)

The Spring Boot application is built and validated using **Jenkins**.

The Jenkins pipeline performs:

- Maven Build
- Unit Testing
- SonarQube Static Analysis
- Artifact Publishing (Nexus Repository)
- Container Image Build (Kaniko)
- Security Scanning
  - Syft
  - Grype
  - OWASP ZAP
- Push Image to Container Registry

---

### Bootstrap Application (GitLab CI/CD)

The Bootstrap application is built using **GitLab CI/CD**.

The GitLab pipeline performs:

- Application Build
- Container Image Build (Kaniko)
- Security Scanning
  - Trivy
  - Gitleaks
- SBOM Generation
- Push Image to Container Registry

---

## 2. GitOps Manifest Update

After a successful build:

- Jenkins updates the image tag inside `spring-boot/deployment.yaml`.
- GitLab CI/CD updates the image tag inside `bootstrap/deployment.yaml`.

Each CI pipeline is responsible only for its own application's deployment manifests.

No deployment commands are executed by either CI pipeline.

---

## 3. Continuous Deployment (CD)

**Argo CD** continuously watches this repository.

Whenever Jenkins or GitLab CI/CD pushes a new manifest update:

- Argo CD detects the Git commit.
- Synchronizes the desired state.
- Applies the updated Kubernetes manifests.
- Deploys the latest application version automatically.

This architecture completely decouples **Continuous Integration (CI)** from **Continuous Deployment (CD)** while ensuring Git remains the only source of deployment truth.

---

# 📂 Repository Structure

```text
.
├── bootstrap/
│   ├── deployment.yaml
│   └── service.yaml
│
├── spring-boot/
│   ├── deployment.yaml
│   └── service.yaml
│
├── kustomization.yaml
└── README.md
```

| Directory | Description |
|------------|-------------|
| **bootstrap/** | Kubernetes manifests for the Bootstrap application |
| **spring-boot/** | Kubernetes manifests for the Spring Boot application |
| **kustomization.yaml** | Root Kustomize configuration consumed by Argo CD |
| **README.md** | Repository documentation |

---

# 📌 Repository Responsibility

This repository is responsible only for Kubernetes deployment manifests.

It does **not** contain:

- Application source code
- CI/CD pipeline definitions
- Dockerfiles
- Build scripts

Its sole purpose is maintaining the desired deployment state for multiple independent applications running on the Kubernetes platform.

---

# 🛠 Technologies

- Kubernetes
- Argo CD
- GitOps
- Kustomize
- Jenkins
- GitLab CI/CD
- Docker
- Kaniko
- Nexus Repository
- SonarQube
- Syft
- Grype
- Trivy
- Gitleaks
- OWASP ZAP

---

# 🔐 Deployment Strategy

Each application owns its own CI pipeline:

- Spring Boot → Jenkins
- Bootstrap → GitLab CI/CD

Both pipelines update this centralized GitOps repository after a successful build.

Argo CD is the **only** component responsible for synchronizing the manifests with the Kubernetes cluster.

This centralized deployment model enables multiple independent applications to share a single GitOps workflow while keeping their CI pipelines completely isolated.

---

# ⭐ Why GitOps?

Using a centralized GitOps repository provides several advantages:

- Single Source of Truth (SSOT)
- Centralized Deployment Management
- Support for Multiple Independent Applications
- Support for Multiple CI Platforms (Jenkins & GitLab CI/CD)
- Clear Separation between CI and CD
- Infrastructure as Code (IaC)
- Fully Version-Controlled Deployments
- Git-Based Rollback Capability
- Automated Kubernetes Synchronization
- Consistent and Reproducible Deployments

---

# 🔗 Related Repositories

| Repository | CI Platform | Purpose |
|------------|-------------|---------|
| **Spring Boot Application** | Jenkins | Spring Boot application source code |
| **Bootstrap Application** | GitLab CI/CD | Bootstrap application source code |
| **CI/CD Platform** | Jenkins + GitLab CI/CD | Central project documentation and platform architecture |
