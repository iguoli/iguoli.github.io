---
title: Systemctl 命令
date: 2020-07-01
modify_date: 2021-11-09
tags: Linux
key: Systemctl-2020-07-01
---

## Unit 命令

### 列出当前内存中的 units

```bash
systemctl list-units
```

> The **LOAD** column shows the load state, one of `loaded`, `not-found`, `bad-setting`, `error`, `masked`. The **ACTIVE** columns shows the general unit state, one of `active`, `reloading`, `inactive`, `failed`, `activating`, `deactivating`. The **SUB** column shows the unit-type-specific detailed state of the unit, possible values vary by unit type.

### Unit 种类

```bash
systemctl --type=help

# equivalent to
systemctl -t help
```

### 列出系统服务类型 units

```bash
systemctl list-units --type=service

# equivalent to
systemctl --type=service
systemctl -t service
```

### Unit 状态

```bash
systemctl --state=help
```

### 列出某种状态的 units

```bash
# State
systemctl --state=active
systemctl --state=inactive

systemctl --state=failed
# equivalent to
systemctl --failed

# Sub state
systemctl --state=running
systemctl --state=exited
```

### 检查一或多个 unit 状态

```bash
# 检查 nginx 和 postgresql-9.6 是否是 active 状态
systemctl is-active nginx postgresql-9.6

# 检查 nginx 是否是 failed 状态
systemctl is-failed nginx
```

### 启动一或多个服务

```bash
systemctl start nginx postgresql-9.6
```

### 停止一或多个服务

```bash
systemctl stop nginx postgresql-9.6
```

### 查看一或多个服务状态

```bash
systemctl status nginx postgresql-9.6
```

### 重载一或多个服务的配置文件

```bash
systemctl reload nginx sshd
```

## Unit File 命令

### 列出系统已安装的 unit files

```bash
systemctl list-unit-files
```

### 查看 postgresql 服务文件状态

```bash
systemctl list-unit-files postgresql-9.6.service
```

### 启用服务

```bash
systemctl enable postgresql-9.6
```

### 禁用服务

```bash
systemctl disable postgresql-9.6
```

### 检查服务是否启用

```bash
systemctl is-enabled postgresql-9.6
```
