---
title: "Mario Bros game, deployed on Kubernetes cluster, managed by Kind, created with Terraform"
date: 2024-06-05
tags:
  - kubernetes
  - helm
  - kind
  - terraform
  - docker
---


In a previous post, we explored [deploying the Mario Bros game on a Kubernetes cluster managed by Civo and created with Terraform](https://veben.github.io/mario-tf-civo-k8s/). This setup is one of the most cost-effective infrastructure options, thanks to Civo's extra-small cluster SKU.
Now, let's see how to achieve the same result using [Kubernetes in Docker (Kind)](https://kind.sigs.k8s.io/), allowing you to run everything locally at no cost.

The associated code is available [here](https://github.com/veben/kind_tf_k8s_mario) 

## Prerequisites
Ensure you have **Docker**, installed in your machine.

## Installation
Follow the instructions from the [Kind Quick Start Guide](https://kind.sigs.k8s.io/docs/user/quick-start/) to install **Kind**:

```sh
[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.23.0/kind-linux-amd64

chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind

kind --version
```

## Overview of the repository
Here's a high-level view of the repository structure:

```plaintext
kind_tf_k8s_mario/
├── providers.tf
├── variables.tf
├── outputs.tf
└── main.tf
```

Each of these files has a specific purpose  in the Terraform configuration:
- `providers.tf`: Configures the necessary providers.
- `variables.tf`: Defines the input variables for the Terraform script.
- `outputs.tf`: Specifies output values.
- `main.tf`: Contains the main configuration for the Kubernetes cluster and Helm chart.

## Cluster Configuration
### 1. Variables
The `variables.tf`, we have a simple configuration with only the cluster name as a configurable variable:
```hcl
variable "cluster_name" {
  description = "Name of the cluster being created"
  type        = string
  default     = "kind-easy-cluster-tf"
}
```

### 2. Providers
In `providers.tf`, we configure the Kind provider:
```hcl
terraform {
  required_providers {
    kind = {
      source  = "tehcyx/kind"
      version = "0.5.1"
    }
  }
}

provider "kind" {
}
```

### 3. Main
In `main.tf`, we define the Kind Kubernetes cluster with 2 nodes: the control pane and the worker:
```hcl
resource "kind_cluster" "default" {
  name           = var.cluster_name
  wait_for_ready = true

  kind_config {
    kind        = "Cluster"
    api_version = "kind.x-k8s.io/v1alpha4"

    node {
      role = "control-plane"
    }

    node {
      role = "worker"
    }
  }
}
```

## Game Components Overview

### 1. Deployment
The deployment manages the Mario Bros application instances:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mario-deployment
spec:
  replicas: 2  # You can adjust the number of replicas as needed
  selector:
    matchLabels:
      app: mario
  template:
    metadata:
      labels:
        app: mario
    spec:
      containers:
      - name: mario-container
        image: sevenajay/mario:latest 
        ports:
        - containerPort: 80
```

This configuration sets up **2** replicas, meaning there will be two different pods running the game. The containers expose port **80**.

### 2. Service
The service exposes the Mario Bros application to external traffic:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mario-service
spec:
  type: NodePort
  selector:
    app: mario
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

This service is of type **NodePort** because Kind does not support LoadBalancer type natively.

## Deploying
### Deploying the cluster
Initialize Terraform, plan the deployment, and apply the configurations:
```sh
terraform init
terraform plan
terraform apply --auto-approve
```

### Deploying the game
Next, deploy the `deployment` and the `service` resources:
```sh
kubectl apply -f k8s_mario/deployment.yaml
kubectl apply -f k8s_mario/service.yaml
```
Verify the existence of the different components:
```sh
kubectl get all
```
Because **NodePort** does not offer external IP, we need to expose the service externally using port forwarding:
```sh
kubectl port-forward service/mario-service 8080:80
```

## Testing
Open a web browser and visit `http://localhost:8080` to access the Mario game.

## Cleaning up
To clean up the resources, destroy the `deployment` and the `service`:
```sh
kubectl delete service mario-service
kubectl delete deployment mario-deployment
```
The, destroy the kind cluster:
```sh
terraform destroy --auto-approve
```