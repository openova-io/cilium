# ADR: Cilium CNI with eBPF

**Status:** Accepted
**Date:** 2024-03-01

## Context

Need a CNI for Kubernetes networking. K3s default (Flannel) lacks advanced features.

## Decision

Use Cilium with eBPF as the CNI, replacing Flannel and kube-proxy.

## Rationale

| Feature | Flannel | Cilium |
|---------|---------|--------|
| Basic networking | ✅ | ✅ |
| Network policies | ❌ | ✅ (L3-L7) |
| Observability | ❌ | ✅ (Hubble) |
| kube-proxy replacement | ❌ | ✅ |
| eBPF performance | ❌ | ✅ |
| Service mesh integration | ❌ | ✅ |

**Key Decision Factors:**
- Native L3-L7 network policies
- Hubble for network observability
- eBPF for better performance
- Replaces kube-proxy

## Configuration

```bash
helm install cilium cilium/cilium \
  --namespace kube-system \
  --set kubeProxyReplacement=true \
  --set k8sServiceHost=${API_SERVER_IP} \
  --set k8sServicePort=6443 \
  --set hubble.enabled=true \
  --set hubble.relay.enabled=true \
  --set hubble.ui.enabled=true
```

## Features Enabled

| Feature | Purpose |
|---------|---------|
| kubeProxyReplacement | Replace kube-proxy with eBPF |
| hubble | Network observability |
| hubble.relay | Metrics export |
| hubble.ui | Visual flow analysis |

## Consequences

**Positive:** Advanced policies, observability, performance, kube-proxy replacement
**Negative:** More complex than Flannel, eBPF kernel requirements

## Related

- [SPEC-CILIUM-CONFIGURATION](./SPEC-CILIUM-CONFIGURATION.md)
- [BLUEPRINT-NETWORK-POLICY](./BLUEPRINT-NETWORK-POLICY.md)
