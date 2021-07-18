---
title: Linux 改键
date: 2021-07-18
modify_date: 2021-07-18
tags: Linux
key: Linux-Keys-Remap-2021-07-18
---

## 安装 [Interception Tools](https://gitlab.com/interception/linux/tools)

**Archlinux**

```zsh
pacman -S interception-tools
```

**Ubuntu**

```zsh
sudo add-apt-repository ppa:deafmute/interception
sudo apt install interception-tools
```

<!--more-->

## 安装 [caps2esc](https://gitlab.com/interception/linux/plugins/caps2esc) 插件

该插件将单击 **Caps** 键映射为 **Esc** 键，长按 **Caps** 键映射为 **Ctrl** 键。

**Archlinux**

```zsh
pacman -S interception-caps2esc
```

**Ubuntu**

```zsh
sudo add-apt-repository ppa:deafmute/interception
sudo apt install interception-caps2esc
```

## 创建 **udevmon** 配置文件

```zsh
vim /etc/udevmon.yaml
```

```yml
- JOB: "intercept -g $DEVNODE | caps2esc | uinput -d $DEVNODE"
  DEVICE:
    EVENTS:
      EV_KEY: [KEY_CAPSLOCK, KEY_ESC]
```

## 创建系统服务

```zsh
vim /etc/systemd/system/udevmon.service
```

```ini
[Unit]
Description=udevmon

[Service]
ExecStart=/usr/bin/nice -n -20 /usr/bin/udevmon -c /etc/udevmon.yaml

[Install]
WantedBy=multi-user.target
```

```zsh
systemctl enable udevmon
systemctl start udevmon
```
