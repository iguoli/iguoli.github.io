---
title: Windows Subsystem Linux
date: 2022-05-18 
modify_date: 2022-05-18 
tags: WSL Windows Linux
key: Windows-Subsystem-Linux-2022-05-18
---

## 查看可安装的 Linux 发行版本

```bash
wsl --list --online
```

## 安装指定发行版本

```bash
wsl --install -d <Distro>
```

## 关机

```bash
wsl --terminate <Distro>
```

<!--more-->

## 删除安装的 Linux 发行版

在 *System Settings*, *Add or remove programs* 可以找到已安装的 Linux 发行版，点击 *Uninstall* 即可。

## 禁止 wsl 添加 Windows 的 PATH 路径

进入 wsl, 创建并编辑 `/etc/wsl.conf` 文件

```ini
[interop]
# enable launch of Windows binaries; default is true
# enabled=false

# append Windows path to $PATH variable; default is true
appendWindowsPath=false
```
