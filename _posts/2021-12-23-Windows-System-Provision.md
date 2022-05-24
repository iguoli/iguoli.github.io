---
title: Windows System Provision
date: 2021-12-23
modify_date: 2021-12-23
tags: Windows Provision
key: Windows-System-Provision-2021-06-25
---

## 安装 [Windows Terminal](https://github.com/microsoft/terminal)

## 安装 [scoop](https://scoop.sh/)

<!--more-->

在 PowerShell 中查看 `PATH` 环境变量

```sh
$env:path

$env:path.Split(';')
```

## 安装 Windows [PowerToys](https://github.com/microsoft/PowerToys)

## 安装 [Dual-Key-Remap](https://github.com/ililim/dual-key-remap)

参考官方[安装步骤](https://github.com/ililim/dual-key-remap#installation)设置改键，并将以下改键写入配置文件

```conf
remap_key=CAPSLOCK
when_alone=CAPSLOCK
with_other=LEFT_CTRL

remap_key=LEFT_SHIFT
when_alone=ESCAPE
with_other=LEFT_SHIFT
```