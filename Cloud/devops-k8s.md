---
title: Kubernetes
description: Notes and understanding about kubernetes with examples
published: true
date: 2025-04-17T14:43:59.301Z
tags: k8s, kubernetes, nginx
editor: markdown
dateCreated: 2025-04-17T14:43:07.426Z
---

## Table of Contents
1. [Introduction](#introduction)
2. [Architecture](#architecture)
3. [Key Components](#key-components)
4. [Deployment](#deployment)
5. [Services](#services)
6. [ConfigMaps and Secrets](#configmaps-and-secrets)
7. [Storage](#storage)
8. [Networking](#networking)
9. [Security](#security)
10. [Monitoring and Logging](#monitoring-and-logging)
11. [Best Practices](#best-practices)

## Introduction

Kubernetes, often abbreviated as K8s, is an open-source container orchestration platform designed to automate the deployment, scaling, and management of containerized applications. It was originally developed by Google and is now maintained by the Cloud Native Computing Foundation (CNCF).

## Architecture

Kubernetes follows a client-server architecture. The master node manages the cluster, while worker nodes run the application workloads.

### Master Node Components
- **API Server**: Exposes the Kubernetes API.
- **etcd**: A consistent and highly-available key-value store used as Kubernetes' backing store for all cluster data.
- **Controller Manager**: Runs controllers that regulate the state of the cluster.
- **Scheduler**: Responsible for scheduling pods to nodes.

### Worker Node Components
- **Kubelet**: An agent that runs on each node in the cluster. It ensures that containers are running in a pod.
- **Kube-proxy**: Maintains network rules on nodes and performs connection forwarding.
- **Container Runtime**: The software that is responsible for running containers (e.g., Docker, containerd).

## Key Components

### Pods
The smallest deployable units of computing that you can create and manage in Kubernetes. A pod can contain one or more containers.

### Nodes
Worker machines in Kubernetes, which can be physical or virtual. Each node runs at least a container runtime, kubelet, and kube-proxy.

### Namespaces
A way to divide cluster resources between multiple users.

## Deployment

Deployments provide declarative updates for Pods and ReplicaSets. You describe the desired state in a Deployment object, and the Kubernetes control plane changes the actual state to the desired state at a controlled rate.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

## Services

A Service in Kubernetes is an abstraction which defines a logical set of Pods and a policy by which to access them.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: MyApp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
```

## ConfigMaps and Secrets

### ConfigMaps
ConfigMaps are used to store non-confidential data in key-value pairs.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config
data:
  key1: value1
  key2: value2
```

### Secrets
Secrets are used to store sensitive information, such as passwords, OAuth tokens, and ssh keys.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  username: YWRtaW4=
  password: MWYyZDFlMmU2N2Rm
```

## Storage

Kubernetes supports various types of storage, including local volumes, network file systems, and cloud provider-specific storage solutions.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

## Networking

Kubernetes provides a variety of networking solutions, including:

- **ClusterIP**: Exposes the Service on an internal IP in the cluster.
- **NodePort**: Exposes the Service on each Node’s IP at a static port.
- **LoadBalancer**: Exposes the Service externally using a cloud provider's load balancer.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: LoadBalancer
  selector:
    app: MyApp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
```

## Security

Kubernetes provides several security features, including:

- **Role-Based Access Control (RBAC)**: Controls access to resources.
- **Network Policies**: Controls the communication between pods.
- **Pod Security Policies**: Defines a set of conditions that a pod must run with in order to be accepted into the system.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

## Monitoring and Logging

Kubernetes integrates with various monitoring and logging tools, such as:

- **Prometheus**: For monitoring.
- **Grafana**: For visualization.
- **ELK Stack (Elasticsearch, Logstash, Kibana)**: For logging.

## Best Practices

1. **Use Namespaces**: To isolate resources for different environments or teams.
2. **Resource Requests and Limits**: Define resource requests and limits to ensure fair usage of cluster resources.
3. **Health Checks**: Use liveness and readiness probes to monitor the health of your applications.
4. **Secrets Management**: Store sensitive information in Secrets rather than hardcoding them in configuration files.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: my-container
    image: my-image
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```

This wiki should provide a comprehensive overview of Kubernetes. If you have any specific questions or need further details, feel free to ask!