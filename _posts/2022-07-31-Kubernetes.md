---
title: Openshift
date: 2022-07-31
modify_date: 2023-06-06
tags: Openshift Kubernetes
key: Openshift-2022-07-31
---

以下命令，如无特别说明，均可以使用 `kubectl` 替换 `oc`
{:.info}

## kubectl 命令自动补全

参考 [kubectl auto completion](https://kubernetes.io/docs/tasks/tools/included/optional-kubectl-configs-bash-linux/) 设置别名和自动补全。

```sh
echo 'source <(kubectl completion zsh)' >>~/.zshrc

echo 'alias k=kubectl' >>~/.zshrc
echo 'complete -o default -F __start_kubectl k' >>~/.zshrc
```

如果使用 `oh-my-zsh`，可以直接使用 `kubectl` 和 `oc` 自动补全插件

<!--more-->

## OC command Cheat Sheet

列出 oc 支持的所有对象

```sh
oc api-resources
```
<!--more-->

### 查看当前集群信息

```sh
oc config view
```

### 获取对象的详细描述

```sh
oc describe pods/nginx

oc describe -f nginx.yaml
```

### Pod 操作

使用标签筛选对象

```sh
oc get pods -l app=nginx
oc get services -l app=nginx
```

使用字段来筛选对象

```sh
oc get pods --field-selector='status.phase=Failed'
oc get pods --field-selector='status.phase!=Succeeded'
```

可用的 `status.phase` 的值有

- `Pending`: The Pod has been accepted by the Kubernetes system, but one or more of its containers is not yet running.

- `Running`: All of the Pod's containers have been created, and at least one container is still running.

- `Succeeded`: All of the Pod's containers have completed their execution successfully, and the Pod has not been configured to restart.

- `Failed`: At least one of the Pod's containers has exited with an error, and the Pod has not been configured to restart.

- `Unknown`: The state of the Pod could not be obtained, typically because of an error communicating with the Kubernetes API server.

为 Pod 应用新标签

```sh
oc label pod $pod_name
```

查看 Pod 日志

```sh
oc logs $pod_name

oc logs -f --tail=50 $pod_name
```

在 Pod 中执行命令

```sh
oc exec $pod_name env
```

进入 Pod 容器中

```sh
oc rsh $pod_name

oc exec -it $pod_name sh
```

删除状态为 `Error` 或不是 `Running|Completed|Creating` 的 pods

```sh
oc delete pods --filed-selector='status.phase=Failed' -n $namespace

oc get pods -n $namespace | awk '$3=="Error" {print $1}' | xargs oc delete pods -n $namespace

oc get pods -n $namespace | awk '$3!~"(Running|Completed|Creating)" {print $1}' | xargs oc delete pods -n $namespace

oc delete pod $(oc get pods | awk '$3=="Error" {print $1}') -n $namespace
```

删除一直是 Terminating 状态的 pods

```sh
oc delete pod $pod_name --grace-period=0 --force -n $namespace

oc get pods -n $namespace | awk '/Terminating/ {print $1}' | xargs oc delete pods --grace-period=0 --force -n $namespace
```

删除当前 Namespace 下的所有 Pods, Secrets, ConfigMaps 等

```sh
oc delete --all pods

oc delete --all secrets

oc delete --all cm

# 删除 Dev namespace 中的所有 deployment
oc delete -all deployments --namespace=Dev
```

### Namespace

创建名为 *Dev* 的 Namespace

```sh
oc create namespace Dev
```

查询 Namespace 中的资源

```sh
# short format
oc get deploy -n Dev

# long format
oc get deploy --namespace=Dev
```

将当前 Namespace 设置为 *Dev*

```sh
oc config set-context --current --namespace=Dev
```

查看当前 Namespace

```sh
oc config view | grep namespace
```

删除 Namespace，需要注意，这会删除该命名空间下的所有内容

```sh
oc delete namespace Dev
```

### [Role Bindings](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html)

Role 和 Role Bindings 可以在集群级别（应用于每个项目）或项目级别（应用于单个项目）设置。

- 集群：
  - **Cluster Role Bindings** 将用户、组或服务帐户绑定到 Cluster Role
  - **Cluster Role** 包含每个项目的权限列表

- 项目：
  - **Role Bindings** 将用户、组或服务帐户绑定到 Role
  - **Role** 包含特定项目中的权限列表

`oc describe role` 和 `oc describe clusterrole` 命令可用于列出角色或集群角色允许的权限。

```sh
oc describe role $admin

oc describe clusterrole $cluster_admin
```

Role Bindings 和 Security Context Constraints 的相似之处在于它们都是访问控制机制。

- Role Bindings 用于控制 OpenShift 用户可以执行的操作
- 安全上下文约束用于控制允许 Pod 执行的操作

#### [管理 Role Bindings](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings)

创建一个新的 role binding，将 `$role_name` 绑定到 `$user_name`

```sh
# Binds a given role to specified users in the current project.
oc adm policy add-role-to-user $role_name $user_name
```

将指定的 `$role_name`从 `$user_name` 中移除

```sh
# Removes a given role from specified users in the current project.
oc adm policy remove-role-from-user $role_name $user_name
```

创建一个新的 cluster role binding，将 `$cluster_role_name` 绑定到 `$user_name`

```sh
# Binds a given role to specified users for all projects in the cluster.
oc adm policy add-cluster-role-to-user $cluster_role_name $user_name
```

将指定的 `$cluster_role_name`从 `$user_name` 中移除

```sh
# Removes a given role from specified users for all projects in the cluster.
oc adm policy remove-cluster-role-from-user $cluster_role_name $user_name
```

`--rolebinding-name=` 选项可以用于 `oc adm policy` 命令，将用户追加到一个已存在的 Cluster Role Bindings.

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

然后通过 `oc` 命令行来创建该 Deployment 对象

```sh
oc apply -f nginx.yaml
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
