# Istio Installation and Bookinfo Application Setup (Step-by-Step)

This document provides complete, copy-pasteable commands to:
- Install Istio on Amazon EKS
- Deploy the Bookinfo sample application
- Enable Istio sidecar injection
- Access the application locally
- Apply traffic shifting rules

---

## Prerequisites

Make sure the following are installed and configured:

- AWS CLI (configured with access to EKS)
- kubectl
- An existing Amazon EKS cluster

Verify cluster access:

```bash
kubectl cluster-info
kubectl get nodes



#Step 1: Install Istio CLI

#Download Istio:

curl -L https://istio.io/downloadIstio | sh -


#Move into the Istio directory:

cd istio-*

#Add istioctl to PATH:

export PATH=$PWD/bin:$PATH

#Verify installation:

istioctl version
----

#Step 2: Install Istio on the EKS Cluster

#Install Istio using the default profile:

istioctl install --set profile=default -y


#Verify Istio components:

kubectl get pods -n istio-system


# You should see:

# istiod

# istio-ingressgateway

# istio-egressgateway

# All should be in Running state.
-----

#Step 3: Create Application Namespace and Enable Sidecar Injection

#Create a namespace for the application:

kubectl create namespace istio-poc

#Enable automatic sidecar injection:

kubectl label namespace istio-poc istio-injection=enabled

#Verify the label:

kubectl get namespace istio-poc --show-labels
----

#Step 4: Deploy the Bookinfo Sample Application

#Deploy the Bookinfo application:

kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml -n istio-poc

#Verify pods:

kubectl get pods -n istio-poc

#Each pod should show:

READY 2/2

#This confirms Istio sidecar injection.
----

# Step 5: Deploy All Bookinfo Service Versions

# Deploy multiple versions of the Bookinfo services:

kubectl apply -f samples/bookinfo/platform/kube/bookinfo-versions.yaml -n istio-poc

# Verify:

kubectl get pods -n istio-poc

# You should see:

# reviews-v1
# reviews-v2
# reviews-v3
----

# Step 6: Verify Services

# Check services running in the namespace:

kubectl get svc -n istio-poc

# All services should expose port 9080.
-----

# Step 7: Access the Application Locally (Port Forward)

Port-forward the productpage service:

kubectl port-forward svc/productpage -n istio-poc 8080:9080


# Open in browser:

http://localhost:8080/productpage

-----

# Step 8: Apply Istio Traffic Management Rules

# Apply the DestinationRule:

kubectl apply -f manifests/destination-rule.yaml


# Apply 50/50 traffic split:

kubectl apply -f manifests/virtualservice-50-50.yaml


# Refresh the product page multiple times to observe:

# Black stars (reviews v2)

# Red stars (reviews v3)
----

# Step 9: Route 100% Traffic (Optional)

# Route all traffic to reviews v2:

kubectl apply -f manifests/virtualservice-100-v2.yaml


# Route all traffic to reviews v3:

kubectl apply -f manifests/virtualservice-100-v3.yaml



