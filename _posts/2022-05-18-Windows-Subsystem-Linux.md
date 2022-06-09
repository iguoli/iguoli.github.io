---
title: Windows Subsystem Linux
date: 2022-05-18 
modify_date: 2022-05-18 
tags: WSL Windows Linux
key: Windows-Subsystem-Linux-2022-05-18
---

## 查看可安装的 Linux 发行版

```sh
wsl --list --online

# short
wsl -l -o
```

## 安装发行版

```sh
wsl --install -d <distro>
```

### 安装 Fedora 发行版

以下内容参考自链接

- [Install Fedora 36 or earlier on Windows Subsystem for Linux (WSL) ](https://dev.to/bowmanjd/install-fedora-on-windows-subsystem-for-linux-wsl-4b26)

首先需要获取 **Fedora rootfs image**，可以从 [Fedora Container Base](https://koji.fedoraproject.org/koji/packageinfo?packageID=26387) 中下载最新版本的 xz 包，当前最新的包为

- [Fedora-Container-Base-36-20220607.0](https://koji.fedoraproject.org/koji/buildinfo?buildID=1977370)

解压 `Fedora-Container-Base-*.tar.xz` 文件，在最下层的文件夹中找到 `layer.tar` 文件，这个文件就是我们需要的 **rootfs image**，可以将其重命令为 `fedora-36-rootfs.tar` 并放在 Downloads 目录下。

然后创建 fedora 的虚拟磁盘目录, 打开 Powershell 输入

```sh
mkdir $HOME\wsl\fedora
```

最后就可以安装 WSL Fedora 发行版了

```sh
wsl --import Fedora-36 $HOME\wsl\fedora $HOME\Downloads\fedora-36-rootfs.tar
```

<!--more-->

### 设置默认启动的发行版

如果安装了多个发行版，可以设置 Fedora-36 为默认启动系统

```sh
wsl --set-default-version Fedora-36

# short
wsl -s Fedora-36
```

### 设置默认登录用户

[为系统添加新用户](https://iguoli.github.io/2017/05/18/Linux-System-Provision.html#为系统添加新用户)，并以该用户登录

```sh
wsl -d Fedora-36 -u user
```

在 `/etc/conf` 中添加配置，使得默认以该用户登录

```ini
[user]
default=user
```

### Fedora 系统微调

#### 安装 man pages

因为我们使用的是精简版的 Fedora 系统，需要手工安装 man pages，首先需要关掉 `/etc/dnf/dnf.conf` 中的 `tsflags=nodocs` 选项。

```sh
grep -v nodocs /etc/dnf/dnf.conf | sudo tee /etc/dnf/dnf.conf
```

安装 `man` 和 `man-pages`

```sh
sudo dnf install -y man man-pages
```

之后由 `dnf install` 安装的包就会安装对应的手册页，但系统之前安装的包仍然没有手册页，比如 `man dnf` 就会显示找不到手册页，因此需要 `sudo dnf reinstall -y dnf` 重新安装才能看到它的手册页。

使用下面的命令可以重新安装所有已存在的包

```sh
for pkg in $(dnf repoquery --installed --qf "%{name}"); do sudo dnf reinstall -qy $pkg; done
```

#### 设置 ping 命令

`ping` 命令位于 `iputils` 安装包内，但安装后会发现 `ping` 命令没有返回结果，这是因为还需要给相应的 group IDs 权限才可以使用 `ping`.

```sh
sudo dnf install -y procps-ng iputils

sudo sysctl -w net.ipv4.ping_group_range="0 2000"
```

第二条命令允许 **0 - 2000** 内的 group IDs 使用 `ping` 命令。通常第一个普通用户的 uid 和 gid 是 1000，可以使用 `getent group` 查看系统所有的 group IDs，或用 `id` 命令查看用户 uid 和 gid。

#### 实用工具

还有一些实用工具，比如 `iproute` 提供了 `ip` 命令，`findutils` 提供了 `find` 命令，`ncurses` 提供了终端常用的库

```sh
sudo dnf install -y iproute findutils ncurses
```

## 关机

```sh
wsl --terminate <distro>

# short
wsl -t <distro>
```

## 删除安装的 Linux 发行版

在 *System Settings*, *Add or remove programs* 可以找到已安装的 Linux 发行版，点击 *Uninstall* 即可。或者执行

```sh
wsl --unregister <distro>
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

```sh
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

```sh
wsl -t <distro>
```

重新进入系统，`unlink /etc/resolv.conf`，这是因为 `/etc/resolv.conf` 文件一般是软链接到 `resolvconf` 生成的文件，最后重新创建 `/etc/resolv.conf` 文件并写入有效的 nameserver。

```sh
unlink /etc/resolv.conf

echo nameserver 1.1.1.1 > /etc/resolv.conf
```
