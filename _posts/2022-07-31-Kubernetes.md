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

## 常用参考链接

[Kubernetes basics in 5 minutes](https://blog.devgenius.io/kubernetes-basics-in-5-minutes-491a6d67448d)