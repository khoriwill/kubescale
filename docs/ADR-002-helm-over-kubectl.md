# ADR-002 — Use Helm Instead of Raw kubectl Manifests

**Status:** Accepted  
**Date:** 2025-05  
**Author:** Khori Williams

---

## Context

Deploying to Kubernetes requires defining resources — Deployments, Services, Ingress rules — as YAML manifests. The question was whether to manage those manifests manually with raw kubectl commands or package them using a higher-level tool.

The system needed to be repeatable, versionable, and deployable with a single command — the same standard applied to Terraform in SecureScale.

---

## Decision

Use **Helm** as the Kubernetes package manager.

Helm packages all Kubernetes resources into a single chart — a versioned, templated, configurable bundle. One command deploys the entire stack. One command upgrades it. One command rolls it back. Values are separated from templates, making the chart reusable across environments.

---

## Alternatives Considered

**Raw kubectl apply** — Applying individual YAML files works but scales poorly. No versioning, no rollback, no single source of truth for configuration. Managing 5+ resources manually is error-prone.

**Kustomize** — Kubernetes-native configuration management. Good for environment-specific overlays but lacks Helm's packaging, versioning, and release management capabilities.

**Terraform Kubernetes provider** — Managing Kubernetes resources via Terraform is possible and keeps tooling consistent with SecureScale. However, Helm is the Kubernetes-native standard and more appropriate for container-focused workflows.

---

## Consequences

✅ One command deploys entire stack — helm install kubescale .  
✅ Built-in versioning — every deploy is a numbered revision  
✅ Instant rollback — helm rollback kubescale 1 reverts to any previous revision  
✅ Values separated from templates — one chart, multiple environments  
✅ Industry standard — the same pattern used in enterprise Kubernetes deployments  
⚠️ Helm templating syntax adds complexity over plain YAML  
⚠️ Chart debugging requires understanding both Helm and Kubernetes layers  
