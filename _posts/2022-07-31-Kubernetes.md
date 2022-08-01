---
title: Kubernetes Cheat Sheet
date: 2022-07-31
modify_date: 2022-07-31
tags: Kubernetes
key: Maven-2022-07-31
---

## 常用资源

列出 kubectl 支持的所有 resources

```sh
kubectl api-resources
```
<!--more-->

| NAME                            | SHORTNAMES | NAMESPACED | KIND                           |
| ------------------------------- | ---------- | ---------- | ------------------------------ |
| bindings                        |            | true       | Binding                        |
| componentstatuses               | cs         | false      | ComponentStatus                |
| **configmaps**                  | cm         | true       | ConfigMap                      |
| **endpoints**                   | ep         | true       | Endpoints                      |
| **events**                      | ev         | true       | Event                          |
| limitranges                     | limits     | true       | LimitRange                     |
| **namespaces**                  | ns         | false      | Namespace                      |
| **nodes**                       | no         | false      | Node                           |
| persistentvolumeclaims          | pvc        | true       | PersistentVolumeClaim          |
| **persistentvolumes**           | pv         | false      | PersistentVolume               |
| **pods**                        | po         | true       | Pod                            |
| podtemplates                    |            | true       | PodTemplate                    |
| replicationcontrollers          | rc         | true       | ReplicationController          |
| resourcequotas                  | quota      | true       | ResourceQuota                  |
| **secrets**                     |            | true       | Secret                         |
| serviceaccounts                 | sa         | true       | ServiceAccount                 |
| **services**                    | svc        | true       | Service                        |
| mutatingwebhookconfigurations   |            | false      | MutatingWebhookConfiguration   |
| validatingwebhookconfigurations |            | false      | ValidatingWebhookConfiguration |
| customresourcedefinitions       | crd,crds   | false      | CustomResourceDefinition       |
| apiservices                     |            | false      | APIService                     |
| controllerrevisions             |            | true       | ControllerRevision             |
| **daemonsets**                  | ds         | true       | DaemonSet                      |
| **deployments**                 | deploy     | true       | Deployment                     |
| **replicasets**                 | rs         | true       | ReplicaSet                     |
| **statefulsets**                | sts        | true       | StatefulSet                    |
| tokenreviews                    |            | false      | TokenReview                    |
| localsubjectaccessreviews       |            | true       | LocalSubjectAccessReview       |
| selfsubjectaccessreviews        |            | false      | SelfSubjectAccessReview        |
| selfsubjectrulesreviews         |            | false      | SelfSubjectRulesReview         |
| subjectaccessreviews            |            | false      | SubjectAccessReview            |
| horizontalpodautoscalers        | hpa        | true       | HorizontalPodAutoscaler        |
| **cronjobs**                    | cj         | true       | CronJob                        |
| **jobs**                        |            | true       | Job                            |
| certificatesigningrequests      | csr        | false      | CertificateSigningRequest      |
| leases                          |            | true       | Lease                          |
| bgpconfigurations               |            | false      | BGPConfiguration               |
| bgppeers                        |            | false      | BGPPeer                        |
| blockaffinities                 |            | false      | BlockAffinity                  |
| clusterinformations             |            | false      | ClusterInformation             |
| felixconfigurations             |            | false      | FelixConfiguration             |
| globalnetworkpolicies           |            | false      | GlobalNetworkPolicy            |
| globalnetworksets               |            | false      | GlobalNetworkSet               |
| hostendpoints                   |            | false      | HostEndpoint                   |
| ipamblocks                      |            | false      | IPAMBlock                      |
| ipamconfigs                     |            | false      | IPAMConfig                     |
| ipamhandles                     |            | false      | IPAMHandle                     |
| ippools                         |            | false      | IPPool                         |
| kubecontrollersconfigurations   |            | false      | KubeControllersConfiguration   |
| networkpolicies                 |            | true       | NetworkPolicy                  |
| networksets                     |            | true       | NetworkSet                     |
| endpointslices                  |            | true       | EndpointSlice                  |
| events                          | ev         | true       | Event                          |
| flowschemas                     |            | false      | FlowSchema                     |
| prioritylevelconfigurations     |            | false      | PriorityLevelConfiguration     |
| ingressclasses                  |            | false      | IngressClass                   |
| ingresses                       | ing        | true       | Ingress                        |
| networkpolicies                 | netpol     | true       | NetworkPolicy                  |
| runtimeclasses                  |            | false      | RuntimeClass                   |
| poddisruptionbudgets            | pdb        | true       | PodDisruptionBudget            |
| podsecuritypolicies             | psp        | false      | PodSecurityPolicy              |
| clusterrolebindings             |            | false      | ClusterRoleBinding             |
| clusterroles                    |            | false      | ClusterRole                    |
| rolebindings                    |            | true       | RoleBinding                    |
| roles                           |            | true       | Role                           |
| priorityclasses                 | pc         | false      | PriorityClass                  |
| csidrivers                      |            | false      | CSIDriver                      |
| csinodes                        |            | false      | CSINode                        |
| csistoragecapacities            |            | true       | CSIStorageCapacity             |
| storageclasses                  | sc         | false      | StorageClass                   |
| volumeattachments               |            | false      | VolumeAttachment               |

## 获取资源的详细描述

```sh
kubectl describe po/nginx

kubectl describe -f nginx.yaml
```

## Kubernetes 对象

Kubernetes 对象是持久化的实体，Kubernetes 使用这些实体去表示整个集群的状态。

### 对象规约 (Spec) 与状态 (Status)

几乎每个 Kubernetes 对象包含两个嵌套的对象字段，它们负责管理对象的配置： 对象 **spec (规约)** 和 对象 **status (状态)**。 对于具有 spec 的对象，你必须在创建对象时设置其内容，描述你希望对象所具有的特征：**期望状态 (Desired State)**。

status 描述了对象的**当前状态 (Current State)**，它是由 Kubernetes 系统和组件设置并更新的。

Kubernetes 控制平面的目标就是保证对象的当前状态与期望状态相一致。

### 描述 Kubernetes 对象

创建 Kubernetes 对象时，必须提供对象的 spec，用来描述该对象的期望状态， 以及关于对象的一些基本信息 (例如名称)。

Kubernetes API 接收 JSON 格式的对象信息。`kubectl` 则可以接收 YAML 格式的对象信息。`kubectl` 在向 Kubernetes API 发起请求时，将这些信息转换成 JSON 格式。

这里有一个 `.yaml` 示例文件，展示了 Kubernetes Deployment 的必需字段和对象 spec：

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx-deployment

spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2
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

然后通过 `kubectl` 命令行来创建该 Deployment 对象

```sh
kubectl apply -f nginx.yaml
```

### 必需字段

在想要创建的 Kubernetes 对象所对应的 `.yaml` 文件中，需要配置的字段如下：

- `apiVersion` - 创建该对象所使用的 Kubernetes API 的版本
- `kind` - 想要创建的对象的类别
- `metadata` - 帮助唯一标识对象的一些数据，包括一个 `name` 字符串、`UID` 和可选的 `namespace`
- `spec` - 你所期望的该对象的状态

对不同的 Kubernetes 对象而言，其 spec 所描述的内容也是不同的，包含了特定于该对象的嵌套字段。 [Kubernetes API 参考](https://kubernetes.io/zh-cn/docs/reference/kubernetes-api/) 给出了所有 Kubernetes 对象的详细信息。

## 常用参考链接

[Kubernetes basics in 5 minutes](https://blog.devgenius.io/kubernetes-basics-in-5-minutes-491a6d67448d)