# Spring Boot App - GitOps Manifests

This repository serves as the **Single Source of Truth (SSOT)** for the Kubernetes deployment manifests of the **Spring Boot Demo** application, managed and synchronized via **Argo CD** in a GitOps workflow.

---

## 🎯 About the Project
This repository is a core component of a complete **CI/CD & GitOps pipeline** built for a production-grade Spring Boot microservice. Instead of letting the CI server (Jenkins) apply changes directly to the cluster, this repo decouples the build process from the deployment state, ensuring that the desired state of the infrastructure is strictly version-controlled in Git.

---

## 🔄 The GitOps Workflow
1. **Continuous Integration (CI):** 
   - Developers push code changes to the [Spring Boot App Repository](https://github.com/Mansourx83/spring-boot-app).
   - **Jenkins** triggers a automated pipeline: builds code, runs Static Analysis (**SonarQube**), publishes artifacts (**Nexus**), builds container images via **Kaniko**, and runs security scans (**Syft, Grype, OWASP ZAP**).
2. **Automated Manifest Update:** 
   - Upon a successful build, Jenkins automatically clones this GitOps repository, updates the container image tag inside `deployment.yaml` to match the latest build number, and pushes the change back to the `main` branch.
3. **Continuous Deployment (CD):** 
   - **Argo CD** continuously monitors this repository. Upon detecting the new image tag commit, it automatically syncs and applies the updates to the Kubernetes cluster.

---

## 📂 Repository Structure

```text
.
├── deployment.yaml     # Kubernetes Deployment manifest (Image tags dynamically updated by Jenkins CI)
└── service.yaml        # Kubernetes Service manifest (Exposes the application inside the cluster)
└── kustomization.yaml  # Argo CD was installed declaratively using Kustomize rather than Helm
