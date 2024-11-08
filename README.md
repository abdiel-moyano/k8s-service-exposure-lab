
# Exposing Applications with Services in Kubernetes

This lab demonstrates how to expose a deployed application in Kubernetes using a **NodePort** service. You will learn about service management in Kubernetes, test basic service types (ClusterIP and NodePort), and understand how to access applications.

## Lab Objectives

- Deploy an application in Kubernetes.
- Create a ClusterIP service for internal communication within the cluster.
- Create a NodePort service to expose the application outside the cluster.

## Prerequisites

- Access to a Kubernetes cluster (Minikube for local testing or a managed cloud cluster).
- `kubectl` installed and configured to connect to your Kubernetes cluster.

## Lab Steps

### 1. Deploy the Example Application

1. Create a Deployment YAML file (`example-deployment.yaml`) for a simple **nginx** application:
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: example-deployment
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: example-app
     template:
       metadata:
         labels:
           app: example-app
       spec:
         containers:
           - name: example-container
             image: nginx
             ports:
               - containerPort: 80
   ```

2. Deploy the application to the cluster:
   ```bash
   kubectl apply -f example-deployment.yaml
   ```

3. Confirm the pod is running:
   ```bash
   kubectl get pods -l app=example-app
   ```

### 2. Create a ClusterIP Service

A **ClusterIP** service provides internal communication within the Kubernetes cluster.

1. Create a ClusterIP service YAML file (`example-clusterip-service.yaml`):
   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: example-clusterip-service
   spec:
     selector:
       app: example-app
     ports:
       - protocol: TCP
         port: 80
         targetPort: 80
   ```

2. Deploy the ClusterIP service:
   ```bash
   kubectl apply -f example-clusterip-service.yaml
   ```

3. Confirm the service was created:
   ```bash
   kubectl get svc example-clusterip-service
   ```

### 3. Create a NodePort Service

A **NodePort** service exposes the application outside the cluster by assigning a port on each node.

1. Create a NodePort service YAML file (`example-nodeport-service.yaml`):
   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: example-nodeport-service
   spec:
     type: NodePort
     selector:
       app: example-app
     ports:
       - protocol: TCP
         port: 80
         targetPort: 80
         nodePort: 30007  # Use a port within the range 30000-32767
   ```

2. Deploy the NodePort service:
   ```bash
   kubectl apply -f example-nodeport-service.yaml
   ```

3. Confirm the NodePort service was created:
   ```bash
   kubectl get svc example-nodeport-service
   ```

### 4. Access the Application

To access the application via NodePort in Minikube, use the following:

1. Run this command to create a tunnel and get the accessible URL:
   ```bash
   minikube service example-nodeport-service
   ```

2. Open the provided URL in a browser or use `curl` to confirm access.

### 5. Clean Up

To delete the resources created in this lab, run:
```bash
kubectl delete -f example-deployment.yaml
kubectl delete -f example-clusterip-service.yaml
kubectl delete -f example-nodeport-service.yaml
```

## Conclusion

This lab provides a foundational understanding of how to use different Kubernetes service types, specifically ClusterIP and NodePort, to manage and access applications.
