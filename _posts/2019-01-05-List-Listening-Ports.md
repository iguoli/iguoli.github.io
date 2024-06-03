---
title: List Listening Ports
date: 2019-01-05 14:43:14
tags: Linux macOS
key: List-Listening-Ports-2019-01-05
---

macOS/Linux 系统下查看系统监听端口的命令。

<!--more-->

## macOS

```sh
lsof -Pn -i4 | grep LISTEN
```

其中，`-P`表示显示 IP 而不是 hostname, `-n`表示显示端口号而不是端口对应的程序名。`-i`用于匹配对应选项的网络地址，`-i4`表示匹配`IPV4`相关网络地址。

## Linux

```sh
netstat -tupln
```

## Windows

```powershell
netstat -an | grep LISTENING
```
