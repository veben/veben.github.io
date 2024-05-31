---
title: "Deploying Mario Bros with Helm on Kubernetes"
date: 2023-12-12
tags:
  - kubernetes
  - helm
---

In a [previous post](https://veben.github.io/mario-k8s/), we explored deploying Mario Bros on a Kubernetes cluster using `kubectl` to apply the associated manifest directly. Today, we'll take a different approach and learn how to deploy the game using **Helm**, leveraging a Helm chart.

For more detailed information, refer to this [repository](https://github.com/veben/helm_charts/tree/main/charts/helm_chart_mario_bros)

## Prerequisites
Before you begin, make sure you have the following available:
- **Kubernetes Cluster**: Ensure you have a running Kubernetes cluster. For that you can follow [this post](https://veben.github.io/civo-cluster/) to easily deploy a k8s cluster with Civo.
- **Helm**: You need helm to manage the chart. You can follow [the official installation link](https://helm.sh/docs/intro/install/).

## Defining the chart
First, clone the [repository](https://github.com/veben/helm_charts/tree/main/charts/helm_chart_mario_bros) containing the Helm chart:

```sh
git clone https://github.com/veben/helm_charts.git
cd helm_charts/charts/helm_chart_mario_bros
```

You can then customize the `values.yaml` file. By default, the deployment contains 2 replicas, and the application is exposed via a service of type **LoadBalancer**.

```yaml
replicaCount: 2

image:
  repository: sevenajay/mario
  pullPolicy: IfNotPresent
  tag: "latest"

nameOverride: ""
fullnameOverride: ""

service:
  type: LoadBalancer
  port: 80
```

<img src="https://raw.githubusercontent.com/veben/helm_charts/main/charts/helm_chart_mario_bros/mario_bros_helm_chart.drawio.svg" alt="mario_bros_helm_chart.drawio.svg" style="width:500px;height:auto;">

## Deploying the game
Deploy the game using Helm with the following command:
```sh
helm install mario-bros ./
```

## Testing the game
After installation, the chart will provide instructions to retrieve the URL to access the game. Copy the URL into a browser to start playing Mario Bros.

<img src="../assets/images/mario-bros-game.png" alt="mario-bros-game" style="width:500px;height:auto;">

## IV. Uninstalling the game
You can uninstall the game with this command:
```sh
helm uninstall mario-bros
```