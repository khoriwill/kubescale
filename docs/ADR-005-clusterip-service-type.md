# ADR-005 — Use ClusterIP as the Service Type

**Status:** Accepted  
**Date:** 2025-05  
**Author:** Khori Williams

---

## Context

Every Kubernetes Service has a type that determines how it is exposed. The three main options are ClusterIP (internal only), NodePort (external via high port), and LoadBalancer (external via cloud load balancer). The service type decision works in conjunction with the Ingress decision in ADR-004.

The system needed a stable internal endpoint for the Ingress controller to forward traffic to — not a directly externally exposed service.

---

## Decision

Use **ClusterIP** as the service type for the kubescale application service.

ClusterIP creates a stable internal IP address reachable only within the cluster. The Ingress controller receives external traffic and forwards it to the ClusterIP service internally. This is the correct production pattern when using an Ingress controller — the service does not need to be directly externally accessible because the Ingress handles all external routing.

---

## Alternatives Considered

**NodePort** — Exposes the service externally on a high port directly. Used during Phase 2 for initial testing before Ingress was configured. Replaced by ClusterIP once Ingress was in place — having both NodePort and Ingress creates redundant and potentially insecure exposure.

**LoadBalancer** — Provisions a cloud load balancer per service. On AWS this means one ELB per service — costly and operationally complex. The correct pattern is one Ingress controller backed by one load balancer routing to multiple ClusterIP services — not one load balancer per service.

**ExternalName** — Maps a service to an external DNS name. Not applicable for this use case — designed for routing to external services, not internal application pods.

---

## Consequences

✅ Internal only — pods are not directly reachable from outside the cluster  
✅ Stable endpoint — ClusterIP address does not change even if pods are replaced  
✅ Correct Ingress pattern — Ingress forwards to ClusterIP, ClusterIP forwards to pods  
✅ Security by design — same defense-in-depth principle as private subnets in SecureScale  
✅ Cost efficient — no cloud load balancer provisioned per service  
⚠️ Not directly reachable from outside cluster without Ingress or port-forward  
⚠️ Requires Ingress controller to be properly configured for external access  

---

## Architecture Pattern
External Traffic
│
▼
Ingress Controller    ← Single external entry point
│
▼
ClusterIP Service     ← Internal stable endpoint
│
▼
Pod 1 / Pod 2         ← Application containers

This layered approach mirrors the SecureScale pattern — ALB (Ingress) in front, EC2 in private subnets (ClusterIP), no direct external access to compute.
