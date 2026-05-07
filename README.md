# KubeScale — Kubernetes Container Orchestration Project

> The next layer of the SecureScale ecosystem — taking production AWS infrastructure and containerizing it with Kubernetes, Helm, and automated CI/CD validation.

---

## What This Is

[SecureScale](https://github.com/khoriwill/securescale-terraform) proved I can architect and deploy production-grade cloud infrastructure on AWS from scratch — VPC, ALB, EC2, ASG, RDS, Terraform IaC, and an AI Ops layer.

KubeScale is the next evolution. The same infrastructure principles — high availability, self-healing, automated deployment — applied to container orchestration with Kubernetes.

This project demonstrates the full modern cloud stack:
SecureScale          →        KubeScale
─────────────────────────────────────────
EC2 instances        →        Pods
Auto Scaling Group   →        Deployment + ReplicaSet
Load Balancer (ALB)  →        Ingress Controller
Terraform IaC        →        Helm Charts
GitHub Actions CI/CD →        GitHub Actions CI/CD

Same patterns. Container-native implementation.

---

## The Problem It Solves

Modern cloud infrastructure runs on containers. Kubernetes is the industry standard for orchestrating them at scale — used by Google, AWS (EKS), Azure (AKS), and virtually every enterprise running containerized workloads.

KubeScale answers a specific question: **Can I take the architecture patterns I proved in SecureScale and operate them in a Kubernetes environment?**

The answer is yes — and this project proves it layer by layer.

---

## What Was Built

### Phase 1 — Local Kubernetes Cluster
Provisioned a fully operational Kubernetes cluster using Minikube. Verified all 7 system components running — etcd, kube-apiserver, kube-scheduler, kube-controller-manager, coredns, kube-proxy, storage-provisioner.

### Phase 2 — Pod → Deployment → Service
Deployed applications the right way — starting with a raw Pod to understand its limitations, then graduating to a Deployment with self-healing replica management, then exposing it as a Service for stable network access.

**Self-healing proven:** Manually deleted a running pod and watched Kubernetes automatically replace it within 15 seconds — zero human intervention. This is the same pattern as ASG in SecureScale, containerized.

### Phase 3 — Ingress + Helm
Replaced NodePort with a proper Ingress controller — routing traffic on port 80 with hostname-based rules, the same pattern as an ALB listener rule in AWS.

Packaged the entire stack — Deployment, Service, Ingress — into a reusable Helm chart. One command deploys everything. One command upgrades everything. Version controlled, repeatable, portable.

### Phase 5 — GitHub Actions CI/CD Pipeline
Every push to main automatically triggers a CI pipeline that lints the Helm chart, validates all rendered templates, and confirms chart metadata — catching errors before they ever reach a cluster.

---

## Architecture
Internet
│
▼
Ingress Controller (nginx)     ← Routes traffic by hostname/path
│
▼
ClusterIP Service              ← Stable internal endpoint
│
▼
ReplicaSet (2 Pods)            ← Self-healing, always 2 running
│
├── Pod 1 (nginx container)
└── Pod 2 (nginx container)

### Kubernetes Components

| Component | Purpose | AWS Equivalent |
|---|---|---|
| Pod | Smallest deployable unit — runs the container | EC2 instance |
| Deployment | Manages desired state — self-heals, scales | Auto Scaling Group |
| ReplicaSet | Ensures N replicas always running | ASG desired capacity |
| Service (ClusterIP) | Stable internal network endpoint | Target Group |
| Ingress | Routes external traffic by rules | Application Load Balancer |
| Helm Chart | Packages all resources for one-command deploy | Terraform module |

---

## Helm Chart Structure
kubescale/
├── .github/
│   └── workflows/
│       └── helm-ci.yaml        # GitHub Actions CI pipeline
├── templates/
│   ├── deployment.yaml         # Pod template + replica management
│   ├── service.yaml            # ClusterIP service
│   ├── ingress.yaml            # Ingress routing rules
│   ├── hpa.yaml                # Horizontal Pod Autoscaler (ready)
│   └── _helpers.tpl            # Reusable template helpers
├── Chart.yaml                  # Chart metadata — name, version, description
├── values.yaml                 # All configuration in one place
└── .helmignore                 # Excludes files from chart packaging

---

## Running It Locally

### Prerequisites
- Minikube installed
- kubectl installed
- Helm v3 installed

### Setup

```bash
# Start your local cluster
minikube start

# Enable ingress controller
minikube addons enable ingress

# Clone the repo
git clone https://github.com/khoriwill/kubescale.git
cd kubescale

# Deploy the entire stack with one command
helm install kubescale .

# Verify everything is running
kubectl get pods,services,ingress
```

### Upgrade After Changes

```bash
# Change anything in values.yaml, then
helm upgrade kubescale .

# Roll back if something goes wrong
helm rollback kubescale 1
```

---

## Self-Healing Demonstration

```bash
# Kill one pod
kubectl delete pod kubescale-66fd478c65-2bctj

# Check again immediately — replacement auto-launched in 15 seconds
kubectl get pods
```

Pod killed. Replacement launched in 15 seconds. Zero human intervention.

---

## Key Lessons Learned

**Raw Pods vs Deployments** — A raw Pod that dies stays dead. A Deployment that loses a Pod immediately launches a replacement. Never run production workloads as raw Pods.

**NodePort vs Ingress** — NodePort exposes services on random high ports. Ingress routes traffic properly on port 80/443 with hostname and path rules — the production pattern.

**Helm vs raw kubectl** — Helm packages everything into a versioned, upgradeable, rollback-capable release. One command deploys, one command rolls back.

---

## What's Next

- Deploy SecureScale web app — replace nginx with the actual SecureScale Docker image
- Horizontal Pod Autoscaler — HPA template already in the chart, ready to activate
- EKS deployment — move from Minikube to AWS EKS using Terraform
- Multi-environment values — separate values-dev.yaml and values-prod.yaml

---

## Related Projects

| Project | Description |
|---|---|
| [securescale-terraform](https://github.com/khoriwill/securescale-terraform) | Production multi-AZ AWS infrastructure |
| [securescale-rag](https://github.com/khoriwill/securescale-rag) | RAG AI knowledge system |
| [anbupath-mvp](https://github.com/khoriwill/anbupath-mvp) | AI-powered career coaching platform |

---

## Author

**Khori Williams**
Technical Program Manager · Cloud & AI Infrastructure · AWS Solutions Architect · CISM

[GitHub](https://github.com/khoriwill) · khoriwill@gmail.com