# Kubernetes Deployment Guide - DevBoard Application

This guide explains how to deploy the **DevBoard** application on a Kubernetes cluster using Kubernetes Deployments and Services.

---

# Prerequisites

Before deploying the application, ensure you have:

- Docker Desktop
- Kubernetes Cluster (KIND, Minikube, or any Kubernetes cluster)
- kubectl installed and configured
- Docker Hub account
- Docker image pushed to Docker Hub

Verify your cluster:

```bash
kubectl get nodes
kubectl cluster-info
```

---

# Project Structure

```
kubedoc/
├── deployment.yaml
├── service.yaml
├── Dockerfile
└── README.md
```

---

# Build the Docker Image

Build the Docker image locally.

```bash
docker build -t devboard:v1 .
```

Verify the image:

```bash
docker images
```

---

# Push the Image to Docker Hub

Login to Docker Hub.

```bash
docker login
```

Tag the image.

```bash
docker tag devboard:v1 <dockerhub-username>/devboard:v1
```

Push the image.

```bash
docker push <dockerhub-username>/devboard:v1
```

Verify the image is available on Docker Hub before deploying.

---

# Create the Deployment

Create a `deployment.yaml` file.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: devboard
spec:
  replicas: 2

  selector:
    matchLabels:
      app: devboard

  template:
    metadata:
      labels:
        app: devboard

    spec:
      containers:
      - name: devboard
        image: <dockerhub-username>/devboard:v1

        ports:
        - containerPort: 4173
```

Deploy the application.

```bash
kubectl apply -f deployment.yaml
```

Verify Deployment.

```bash
kubectl get deployments
```

Verify Pods.

```bash
kubectl get pods
```

Describe the Deployment.

```bash
kubectl describe deployment devboard
```

---

# Expose the Application

Create a `service.yaml` file.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: devboard-service

spec:
  selector:
    app: devboard

  ports:
    - protocol: TCP
      port: 80
      targetPort: 4173

  type: NodePort
```

Apply the Service.

```bash
kubectl apply -f service.yaml
```

Verify Services.

```bash
kubectl get services
```

---

# Access the Application

If using KIND:

Port-forward the Service.

```bash
kubectl port-forward service/devboard-service 6767:80
```

Open your browser.

```
http://localhost:6767
```

---

# Scaling the Deployment

Scale to 5 replicas.

```bash
kubectl scale deployment devboard --replicas=5
```

Verify.

```bash
kubectl get pods
```

---

# Rolling Update

After pushing a new Docker image:

```bash
docker build -t <dockerhub-username>/devboard:v2 .
docker push <dockerhub-username>/devboard:v2
```

Update the Deployment.

```bash
kubectl set image deployment/devboard devboard=<dockerhub-username>/devboard:v2
```

Check rollout status.

```bash
kubectl rollout status deployment/devboard
```

---

# Rollback

View rollout history.

```bash
kubectl rollout history deployment/devboard
```

Rollback to the previous version.

```bash
kubectl rollout undo deployment/devboard
```

---

# Useful Commands

View all resources.

```bash
kubectl get all
```

View Pods.

```bash
kubectl get pods
```

View Deployments.

```bash
kubectl get deployments
```

View Services.

```bash
kubectl get services
```

Describe a Pod.

```bash
kubectl describe pod <pod-name>
```

View Pod logs.

```bash
kubectl logs <pod-name>
```

Execute into a Pod.

```bash
kubectl exec -it <pod-name> -- /bin/sh
```

Delete a Pod.

```bash
kubectl delete pod <pod-name>
```

---

# Troubleshooting

## ErrImagePull

The image could not be downloaded.

Common causes:

- Incorrect Docker image name
- Incorrect image tag
- Image not pushed to Docker Hub
- Private repository without credentials

---

## ImagePullBackOff

Kubernetes failed to pull the image and is retrying with increasing delays.

Check the Pod events.

```bash
kubectl describe pod <pod-name>
```

---

## CrashLoopBackOff

The container starts but crashes repeatedly.

View logs.

```bash
kubectl logs <pod-name>
```

---

## Pending

The Pod is waiting to be scheduled.

Check cluster resources.

```bash
kubectl describe pod <pod-name>
```

---

# Clean Up

Delete the Deployment.

```bash
kubectl delete deployment devboard
```

Delete the Service.

```bash
kubectl delete service devboard-service
```

Delete all resources.

```bash
kubectl delete -f .
```

---

# Notes

- Use **Deployments** for stateless applications.
- Store container images in Docker Hub or another container registry.
- Expose applications using Services.
- Use `kubectl describe` to diagnose deployment issues.
- Use `kubectl logs` to troubleshoot application errors.
- Prefer Services over directly accessing Pods because Pod IPs are ephemeral.
