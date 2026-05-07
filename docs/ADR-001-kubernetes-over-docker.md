# ADR-001 — Use Kubernetes Instead of Raw Docker

**Status:** Accepted  
**Date:** 2025-05  
**Author:** Khori Williams

---

## Context

The SecureScale web application was already containerized using Docker and published to Docker Hub. The next decision was how to orchestrate and run those containers in a way that demonstrates production-grade infrastructure thinking — not just "docker run" on a single machine.

The system needed self-healing, scaling, and declarative deployment — the same guarantees the EC2 Auto Scaling Group provided in SecureScale, but for containers.

---

## Decision

Use **Kubernetes** as the container orchestration platform.

Kubernetes manages containers at scale — scheduling them across nodes, restarting failed containers, scaling replicas up and down, and routing traffic to healthy pods. It is the industry standard for production container orchestration used by AWS (EKS), Google (GKE), and Azure (AKS).

---

## Alternatives Considered

**Raw Docker (docker run / docker-compose)** — Simple to operate but no self-healing, no automatic scaling, no declarative desired state. If a container dies, nothing restarts it. Not appropriate for production workloads.

**Docker Swarm** — Docker's native orchestration. Simpler than Kubernetes but significantly less adoption in enterprise environments. Limited ecosystem, fewer managed cloud offerings, declining industry usage.

**AWS ECS (Elastic Container Service)** — AWS-native container orchestration. Strong for teams already deep in AWS but less portable than Kubernetes and less transferable as a skill across cloud providers.

**AWS EKS only** — Running straight to EKS without local Kubernetes experience would skip foundational understanding. Minikube first, EKS as the production target.

---

## Consequences

✅ Self-healing — failed pods are automatically replaced, proven in Phase 2  
✅ Declarative desired state — define what you want, Kubernetes makes it happen  
✅ Industry standard — directly transferable to EKS, GKE, AKS  
✅ Rich ecosystem — Helm, Ingress controllers, HPA, and more built on top  
⚠️ Steeper learning curve than Docker Compose or raw Docker  
⚠️ Overkill for single-container, single-machine deployments  
