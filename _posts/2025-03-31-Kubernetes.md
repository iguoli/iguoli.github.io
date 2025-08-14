---
title: Kubernetes 命令
date: 2025-03-31
modify_date: 2025-04-09
tags: Kubernetes DevOps
key: Kubernetes-2025-03-31
---

## Kubernetes 命令

### config 命令

合并 kube config 文件到当前 config

```sh
KUBECONFIG=~/.kube/config:~/kubeconf kubectl config view --merge --flatten > ~/merged_config

mv ~/merged_config ~/.kube/config
```

### 常用 pods 命令

```sh
# 列出所有不是 Running 状态的 pods
kubectl get pods --all-namespaces --field-selector status.phase!=Running

```sh
# 本地端口转发
kubectl port-forward service/<service-name> 8080:80
```

<!--more-->

获取 k8s 上的资源列表

```sh
kubectl api-resources
```

查看 k8s 支持的 API 资源及其相关字段的解释

```sh
kubectl explain deployment

kubectl explain pods.spec.containers.resources.requests
```

`requests` 表示 **容器正常运行时需要的最小资源量**，它有两个作用：

调度器依据：Kubernetes 调度器会根据 Pod 的 request 值，决定将其调度到哪一台 Node 上，以确保有足够的资源。

指标参考：HorizontalPodAutoscaler 也会用 request 值来计算资源使用率（如 CPU/Memory utilization）。

```yaml
resources:
  requests:
    cpu: "200m"
    memory: "512Mi"
```

### Kubernetes Secret

#### Prepare the Certificate and Key

- tls.crt
- tls.key

#### Create a TLS Secret

Create a secret in the same namespace as your Ingress:

```bash
kubectl create secret tls my-tls-secret \
  --cert=./tls.crt \
  --key=./tls.key \
  -n default
```

#### Reference the Secret in the Ingress

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
  namespace: default
spec:
  tls:
    - hosts:
        - example.com
      secretName: my-tls-secret
  rules:
    - host: example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: my-service
                port:
                  number: 80
```