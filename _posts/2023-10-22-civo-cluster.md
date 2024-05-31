---
title: "Deploying a Kubernetes Cluster with Civo"
date: 2023-10-22
tags:
  - kubernetes
  - civo
---

[Civo](https://www.civo.com) is a cloud platform designed for simplicity and speed, offering one of the easiest ways to leverage Kubernetes clusters. It's a convenient solution for testing new features on Kubernetes  due to its fast deployment capabilities. You can have a basic cluster up and running in less than two minutes. Moreover, Civo provides cost-effective infrastructure options, including **extra-small** clusters, and offers new users **$250** in credit for the first month.

## Getting Started with Civo
### 1. Create Your Account
Sign up on the Civo platform. Once your account is validated by the Civo team, you can proceed to set up your **API key**.

### 2. Set Up Your API Key
After downloading and installing the Civo CLI, add your API key as an environment variable:

```sh
civo apikey add civo-key ${CIVO_API_KEY}
civo apikey current civo-key
```

### 3. Define Environment Variables
Prepare the necessary environment variables for your cluster:

```sh
CLUSTER_NAME="civo-easy-cluster"
CLUSTER_NODES=1
CLUSTER_SIZE="g4s.kube.xsmall"
CLUSTER_REGION="FRA1"
```

### 4. Create Your Cluster
With a single command, you can create your Kubernetes cluster:

```sh
civo kubernetes create ${CLUSTER_NAME} \
--size=${CLUSTER_SIZE} \
--nodes=${CLUSTER_NODES} \
--region=${CLUSTER_REGION} \
--wait
```

## Managing your cluster
- **Visualize Your Cluster**: Check the status and details of your newly created cluster on the [Civo dashboard](https://dashboard.civo.com/kubernetes)

- **Access Your Cluster via CLI**: Export the configuration to access your cluster using CLI tools like `kubectl` or `k9s`:

```sh
civo --region=${CLUSTER_REGION} \
kubernetes config ${CLUSTER_NAME} > ./config.yaml

export KUBECONFIG=$PWD/config.yaml
```

## Deleting your cluster
- Finally, do not forget to delete the cluster easily like that:

```sh
CLUSTER_NAME="civo-easy-cluster"
CLUSTER_REGION="FRA1"
civo kubernetes remove ${CLUSTER_NAME} --region=${CLUSTER_REGION} --yes
```

- For more detailed information, refer to this [repository](https://github.com/veben/civo_k8s_easy_cluster)