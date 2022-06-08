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

在 *System Settings*, *Add or remove programs* 可以找到已安装的 Linux 发行版，点击 *Uninstall* 即可。或者执行

```bash
wsl --unregister <Distro>
```

## 禁止 wsl 添加 Windows 的 PATH 路径

进入 wsl, 创建并编辑 `/etc/wsl.conf` 文件

```ini
[interop]
# enable launch of Windows binaries; default is true
# enabled=false

# append Windows path to $PATH variable; default is true
appendWindowsPath=false
```

## WSL 网络

### 使用代理访问外部网络

WSL 安装 distro 后，在 distro 系统内可以 ping 通 Windows 的主机 IP 地址，因此，可以在 Windows 上启动代理服务，并在 distro 中设置环境变量，即可访问外部网络。

```bash
export http_proxy=http://host-ip:7890
export https_proxy=http://host-ip:7890
export all_proxy=socks5://host-ip:7891
```

### 设置 */etc/resolv.conf* 文件

WSL 会自动为 distro 创建 `/etc/resolv.conf` 文件，且随机分配一个 nameserver IP 地址。可以设置 `/etc/wsl.conf` 文件禁止系统自动分配 nameserver。

```ini
[network]
# turn off generation of /etc/resolv.conf
generateResolvConf=false
```

保存后退出 wsl 并关闭系统

```bash
wsl -t <Distro>
```

重新进入系统，`unlink /etc/resolv.conf`，这是因为 `/etc/resolv.conf` 文件一般是软链接到 `resolvconf` 生成的文件，最后重新创建 `/etc/resolv.conf` 文件并写入有效的 nameserver。

```bash
unlink /etc/resolv.conf

echo nameserver 1.1.1.1
```
