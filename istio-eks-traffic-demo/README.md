# Istio Service Mesh on Amazon EKS (PoC)

This repository demonstrates a simple end-to-end Istio Service Mesh
setup on Amazon EKS, including traffic shifting between multiple
versions of a service without redeploying applications.

---

## What This Demo Shows

- Amazon EKS cluster connectivity
- Istio control plane running
- Application pods with Istio sidecar injection
- Traffic routing using Istio VirtualService and DestinationRule
- 50/50 traffic split (canary deployment)
- Secure local access using kubectl port-forward

---

## Architecture Overview

Each application pod contains:
- Application container
- Istio Envoy sidecar proxy

The sidecar intercepts all service-to-service traffic and applies
Istio routing rules.

---



### Explanation of the Screenshot (Start → End)

1. **kubectl cluster-info**  
   Confirms connection to the Amazon EKS control plane.

2. **kubectl get nodes**  
   Shows all worker nodes in Ready state.

3. **kubectl get pods -n istio-system**  
   Confirms Istio control plane components are running.

4. **kubectl get pods -n istio-poc**  
   Shows application pods with `2/2` containers, confirming
   Istio sidecar injection.

5. **kubectl get svc -n istio-poc**  
   Displays internal services running on port 9080.

6. **kubectl port-forward**  
   Maps `localhost:8080` to the productpage service for local access.

7. **Browser Access**  
   Refreshing the page shows black and red stars, proving
   live traffic shifting.

---

## Traffic Shifting Demo

The `reviews` service has multiple versions:
- v2 → black stars
- v3 → red stars

Using Istio VirtualService, traffic is split 50/50 between v2 and v3.
Each refresh sends traffic to a different version based on weights.

---

## Why This Matters

- No application redeployment
- No pod restart
- Traffic controlled at the network layer
- Safe canary deployments and A/B testing


