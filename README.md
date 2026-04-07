# kubernetes-practice

A hands-on Kubernetes practice repository containing YAML manifests for deploying and connecting a full stack environment — **Nginx**, **MySQL**, **phpMyAdmin**, and a **Node.js + MySQL** application — across isolated service deployments on a Kubernetes cluster.

---

## What's Included

| Directory    | Description                                                     |
|--------------|-----------------------------------------------------------------|
| `nginx/`     | Nginx web server — Deployment, Service, and ConfigMap manifests |
| `mysql/`     | MySQL database — Deployment, Service, Secret, and PVC manifests |
| `phpadmin/`  | phpMyAdmin — Deployment and Service for visual DB management    |
| `node-mysql/`| Node.js app with MySQL connectivity — Deployment and Service    |

---

## Architecture

```
            ┌─────────────┐
            │    Nginx    │  ← Web / Reverse Proxy
            └──────┬──────┘
                   │
     ┌─────────────┼─────────────┐
     │             │             │
┌────┴────┐  ┌─────┴──────┐  ┌──┴──────────┐
│  MySQL  │  │ node-mysql  │  │ phpMyAdmin  │
│  (DB)   │  │  (Node.js) │  │  (DB UI)    │
└─────────┘  └────────────┘  └─────────────┘
```

All services communicate within the cluster via Kubernetes ClusterIP services and DNS-based service discovery.

---

## Prerequisites

- A running Kubernetes cluster (local: [Minikube](https://minikube.sigs.k8s.io/) / [Kind](https://kind.sigs.k8s.io/), or cloud: EKS / GKE / AKS)
- [kubectl](https://kubernetes.io/docs/tasks/tools/) configured and connected to your cluster

---

## Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/ujjwolthapa/kubernetes-practice.git
cd kubernetes-practice
```

### 2. Deploy Each Service

Apply the manifests in order — MySQL first since other services depend on it.

#### MySQL

```bash
kubectl apply -f mysql/
```

#### phpMyAdmin

```bash
kubectl apply -f phpadmin/
```

#### Nginx

```bash
kubectl apply -f nginx/
```

#### Node.js + MySQL App

```bash
kubectl apply -f node-mysql/
```

### 3. Verify Everything is Running

```bash
kubectl get pods
kubectl get services
```

All pods should show `Running` status.

---

## Service Details

### `mysql/`
Deploys a MySQL database instance with:
- A `Deployment` running the MySQL container
- A `ClusterIP` Service exposing port `3306` within the cluster
- A `Secret` for storing database credentials securely
- A `PersistentVolumeClaim` for durable database storage

### `nginx/`
Deploys an Nginx web server with:
- A `Deployment` for the Nginx container
- A `Service` to expose the web server (NodePort or ClusterIP)
- Optional `ConfigMap` for custom Nginx configuration

### `phpadmin/`
Deploys phpMyAdmin connected to the MySQL service with:
- A `Deployment` referencing the MySQL `ClusterIP` service
- A `Service` to expose the phpMyAdmin UI

### `node-mysql/`
Deploys a Node.js application that connects to the MySQL database with:
- A `Deployment` for the Node.js container
- A `Service` to expose the application
- Environment variables pointing to the MySQL service hostname and credentials

---

## Accessing Services

### phpMyAdmin

If exposed as `NodePort`:
```bash
minikube service phpadmin-service
# or
kubectl port-forward svc/phpadmin-service 8080:80
```
Then open: `http://localhost:8080`

### Node.js App

```bash
kubectl port-forward svc/node-mysql-service 3000:3000
```
Then open: `http://localhost:3000`

### Nginx

```bash
kubectl port-forward svc/nginx-service 8081:80
```
Then open: `http://localhost:8081`

---

## Useful kubectl Commands

```bash
# View all resources
kubectl get all

# Check logs for a pod
kubectl logs <pod-name>

# Describe a resource for debugging
kubectl describe pod <pod-name>

# Delete all resources in a directory
kubectl delete -f mysql/
kubectl delete -f nginx/
kubectl delete -f phpadmin/
kubectl delete -f node-mysql/

# Get into a running pod
kubectl exec -it <pod-name> -- /bin/sh
```

---

## Concepts Practiced

- Kubernetes **Deployments** and **ReplicaSets**
- **Services** — ClusterIP, NodePort
- **Secrets** for sensitive data (DB credentials)
- **PersistentVolumeClaims** for stateful storage
- **ConfigMaps** for externalizing configuration
- Inter-service communication via **DNS-based service discovery**
- Multi-service orchestration on a single cluster

---

## License

This project is open source. See the [LICENSE](LICENSE) file for details.
