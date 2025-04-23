---
title: Kubernetes/Helm 命令
date: 2025-03-31
modify_date: 2025-04-09
tags: Kubernetes Helm DevOps
key: Kubernetes-Helm-2025-03-31
---

## Kubernetes 命令

### 常用 pods 命令

```sh
# 列出所有不是 Running 状态的 pods
kubectl get pods --all-namespaces --field-selector status.phase!=Running

```sh
# 本地端口转发
kubectl port-forward service/<service-name> 8080:80
```

<!--more-->

## Helm 命令

Manage the releases of the charts that are deployed on the Kubernetes cluster. Examples:

```sh
# list the releases that are deployed in the current namespace
# aliases: list, ls
helm ls

# list the releases that are deployed in all namespaces
helm ls -A

# show the status of the named release in a namespace
helm status my-elasticsearch -n elk

# download the values of the named release
helm get values my-elasticsearch -n elk > values.yaml

# upgrade or install the named release of the chart in a namespace
# and use the values from the values.yaml file
helm upgrade --install my-elasticsearch elastic/elasticsearch -n elk --create-namespace -f values.yaml

# uninstall the named release of the chart in a namespace
# aliases: uninstall, del, delete
helm del my-elasticsearch -n elk
```

Show the information about the charts that is available in the repositories configured on the local machine. Examples:

```sh
# list all available charts on the local repositories
helm search repo

# search for stable charts matching the keyword "nginx"
helm search repo prometheus

# show the information of the elastic/kibana chart
helm show chart elastic/kibana

# show the default values of the elastic/elasticsearch chart
helm show values elastic/elasticsearch
```

Manage the chart repositories that are configured on the local machine. Examples:

```sh
# list chart repositories that are configured on the local machine
# aliases: list, ls
helm repo ls

# remove the elasticsearch chart repositories
# aliases: remove, rm
helm repo rm elasticsearch
```