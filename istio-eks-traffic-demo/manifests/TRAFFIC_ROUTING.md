# Istio Traffic Management Manifests

This folder contains Istio configuration files used to control
service-to-service traffic for the Bookinfo application running
on Amazon EKS.

These manifests demonstrate how Istio can route traffic between
multiple versions of a service without redeploying applications.

---

## destination-rule.yaml

### Purpose
Defines the available versions (subsets) of the `reviews` service.

### What It Does
- Maps logical version names (v2, v3) to Kubernetes pod labels
- Tells Istio how to identify each service version

### Why It Is Required
Istio VirtualService references subsets (v2, v3).  
Without a DestinationRule, Istio does not know which pods belong
to which version.

### Key Concept
subset → pod labels → actual running pods


This file must be applied **before** traffic routing rules.

---

## virtualservice-50-50.yaml

### Purpose
Splits live traffic between two service versions.

### What It Does
- Routes 50% of traffic to `reviews v2`
- Routes 50% of traffic to `reviews v3`

### Why It Is Used
Demonstrates:
- Canary deployments
- A/B testing
- Safe rollout of new versions

### Expected Result
Refreshing the product page shows:
- Black stars (v2)
- Red stars (v3)

This proves dynamic traffic shifting without redeployment.

---

## virtualservice-100-v2.yaml

### Purpose
Routes all traffic to `reviews v2`.

### What It Does
- Sends 100% of requests to the stable version (v2)
- Completely disables traffic to other versions

### Why It Is Used
- Rollback to a stable version
- Force traffic to a known-good release

### Expected Result
Refreshing the product page always shows:
- Black stars only

---

## virtualservice-100-v3.yaml

### Purpose
Routes all traffic to `reviews v3`.

### What It Does
- Sends 100% of requests to the new version (v3)
- Bypasses other versions

### Why It Is Used
- Full rollout of a new version
- After successful canary testing

### Expected Result
Refreshing the product page always shows:
- Red stars only

---

## Execution Order (Important)

1. Apply DestinationRule:

$ kubectl apply -f destination-rule.yaml

2. Apply one VirtualService at a time:

$ kubectl apply -f virtualservice-50-50.yaml

OR

$ kubectl apply -f virtualservice-100-v2.yaml

OR

$ kubectl apply -f virtualservice-100-v3.yaml

<!-- Summary

DestinationRule defines service versions

VirtualService controls traffic routing

Traffic changes are applied instantly

No pods are restarted

No application code is changed -->
