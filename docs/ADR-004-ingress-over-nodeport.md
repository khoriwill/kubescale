# ADR-004 — Use Ingress Instead of NodePort

**Status:** Accepted  
**Date:** 2025-05  
**Author:** Khori Williams

---

## Context

Kubernetes services need to be accessible from outside the cluster. Three options exist for exposing services externally — NodePort, LoadBalancer, and Ingress. The choice directly impacts how traffic enters the cluster, what ports are used, and how routing rules are managed.

The system needed production-style traffic routing on standard ports with hostname-based rules — the same pattern as an ALB in SecureScale.

---

## Decision

Use an **Ingress controller** with hostname-based routing rules.

Ingress exposes HTTP/HTTPS routes from outside the cluster to services inside it. The nginx Ingress controller watches for Ingress resources and configures routing rules — traffic comes in on port 80/443, gets matched against hostname and path rules, and is forwarded to the correct service. This is the production pattern.

---

## Alternatives Considered

**NodePort** — Exposes a service on a static port in the 30000-32767 range on every node. Simple to configure but uses non-standard ports, requires firewall rules for each service, and does not support hostname-based routing. Used in Phase 2 for initial testing — replaced by Ingress for production pattern.

**LoadBalancer service type** — Provisions an external load balancer automatically. On AWS this creates an ELB per service — expensive and operationally heavy when running multiple services. Ingress consolidates all routing through a single load balancer.

**Port-forward only** — kubectl port-forward tunnels directly to a pod for development testing. Not a production pattern — requires a running kubectl session and bypasses normal traffic routing.

---

## Consequences

✅ Standard ports — traffic on port 80/443, no random high ports  
✅ Hostname-based routing — nginx.local routes to the correct service  
✅ Single entry point — one Ingress controller handles all services  
✅ Direct AWS parallel — same concept as ALB listener rules in SecureScale  
✅ TLS termination ready — SSL can be added at the Ingress layer  
⚠️ Requires an Ingress controller deployed in the cluster  
⚠️ Local testing requires minikube tunnel on Docker driver  
⚠️ More configuration than NodePort for simple single-service setups  
