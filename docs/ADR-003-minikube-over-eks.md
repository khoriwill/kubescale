# ADR-003 — Use Minikube for Development Instead of EKS

**Status:** Accepted  
**Date:** 2025-05  
**Author:** Khori Williams

---

## Context

Kubernetes can run locally via Minikube or in the cloud via managed services like AWS EKS. The decision was where to build and validate the KubeScale project — locally first or directly on a cloud cluster.

The project needed to stay within free tier constraints while still demonstrating real Kubernetes competency.

---

## Decision

Use **Minikube** for the development and portfolio demonstration environment.

Minikube runs a single-node Kubernetes cluster entirely on the local machine — no cloud costs, no AWS account dependencies, full Kubernetes API compatibility. Every concept learned and proven in Minikube transfers directly to EKS.

---

## Alternatives Considered

**AWS EKS** — The production target. Managed Kubernetes on AWS — highly available, multi-node, integrated with IAM, ALB, and other AWS services. However, EKS costs approximately $0.10/hour for the control plane plus EC2 node costs. Not free-tier safe for extended development.

**k3s** — Lightweight Kubernetes distribution designed for resource-constrained environments. Faster and lighter than Minikube but less feature-complete and less representative of a full Kubernetes environment.

**Kind (Kubernetes in Docker)** — Runs Kubernetes nodes as Docker containers. Good for CI testing but less straightforward for local development with ingress and networking.

**Docker Desktop Kubernetes** — Built into Docker Desktop on Mac/Windows. Convenient but not available in WSL/Linux environments and less configurable than Minikube.

---

## Consequences

✅ Zero cost — runs entirely on local machine, no AWS charges  
✅ Full Kubernetes API — every kubectl and helm command works identically to EKS  
✅ Addon ecosystem — ingress, metrics-server, dashboard all available via minikube addons  
✅ Fast iteration — no cloud provisioning wait time  
⚠️ Single node only — cannot demonstrate multi-node scheduling or node failure  
⚠️ Networking differences — minikube tunnel required for ingress on Docker driver  
⚠️ Not production — EKS is the production path and the natural next step  

---

## Migration Path

Moving from Minikube to EKS is a infrastructure swap, not a code change. The same Helm chart deploys to EKS without modification — only the cluster target changes. Terraform provisions the EKS cluster, kubectl context switches to it, and helm install runs identically.
