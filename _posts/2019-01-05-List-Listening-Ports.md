---
title: 查看系统监听端口
date: 2019-01-05 14:43:14
tags: linux macos
key: 2019-01-05-List-Listening-Ports
---

## MacOS

```zsh
lsof -Pn -i4 | grep LISTEN
```

其中，`-P`表示显示 IP 而不是 hostname, `-n`表示显示端口号而不是端口对应的程序名。`-i`用于匹配对应选项的网络地址，`-i4`表示匹配`IPV4`相关网络地址。

## Linux

```zsh
netstat -tupln
```

## Windows

```cmd
netstat -an | grep 'LISTENING'
```