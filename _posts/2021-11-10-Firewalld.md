---
title: Linux firewalld 设置
date: 2021-11-10
modify_date: 2021-11-26
tags: Linux Firewall
key: Firewalld-2021-11-10
---

## CentOS 8 防火墙设置

### 防火墙状态

```sh
systemctl status firewalld
```

### 列出防火墙可设置的服务名

```sh
firewall-cmd --get-services
```

<!--more-->

### 允许 http, https 和 postgresql 数据库的网络访问

```sh
firewall-cmd --add-service=http --add-service=https --add-service=postgresql --permanent
```

注意： 使用 `--permanent` 参数后，新配置会写入配置文件，但不会在当前运行环境立即生效，需要 firewalld 服务重启后才能生效。不带 `--permanent` 参数则会使新配置立即应用到当前运行环境中。如果希望使新参数即能永久生效，又能马上在当前运行环境中生效，可以分别执行一次带和不带 `--permanent` 参数的命令。
{:.info}

如果在当前运行环境应用了一些新配置，但没有使用 `--permanent`，可以使用 `--runtime-to-permanent` 参数将当前配置写入到配置文件，使其永久生效。

```sh
firewalld-cmd --add-service=http

firewalld-cmd --runtime-to-permanent
```

如果需要重新加载配置文件，可以使用 `--reload` 参数

```sh
firewalld-cmd --add-service=http --permanent

firewalld-cmd reload
```

### 取消 http 访问

```sh
firewall-cmd --remove-service=http --permanent
```

### 允许 8080 和 8800 端口的 TCP 访问

```sh
firewall-cmd --add-port=8080/tcp --add-port=8800/tcp --permanent
```

### 取消 8800 端口的 TCP 访问

```sh
firewall-cmd --remove-port=8800/tcp
```

### 显示默认 zone 的详细信息

```sh
firewall-cmd --list-all
```
