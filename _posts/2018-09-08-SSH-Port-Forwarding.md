---
title: SSH 端口转发
date: 2018-09-08
modify_date: 2019-07-17
tags: SSH
key: 2018-09-08-SSH-Port-Forwarding
---

以下端口转发命令都可以加上 `-f`、`-N` 和 `-T` 选项，`-f` 表示让 ssh 进入后台执行命令，`-N` 表示不执行远程命令，`-T` 表示不分配终端。

> `-f`  
> Requests ssh to go to background just before command execution.  This is useful if ssh is going to ask for passwords or passphrases, but the user wants it in the background.  This implies -n.  The recommended way to start X11 programs at a remote site is with something like ssh -f host xterm.
>
> `-N`  
> Do not execute a remote command.  This is useful for just forwarding ports.
>
> `-T`  
> Disable pseudo-terminal allocation.

## 本地端口转发

在 `OpenSSH` 中，可以使用 `-L` 选项打开本地端口转发

```zsh
ssh -L [bind-address]:<local-port>:<remote-server>:<remote-port> <jump-server>
```

下面的命令创建一个到 jump.server 的连接，通过 jump.server 跳转，将所有到客户端 80 端口的连接转发到 remote.server 的 80 端口上

```zsh
ssh -L 80:remote.server:80 jump.server
```

如果不需要跳转，可以将 jump.server 改为 remote.server，并将远程端口绑定在 localhost 或 127.0.0.1 上

```zsh
ssh -L 80:127.0.0.1:80 remote.server
ssh -L 80:localhost:80 remote.server
```

默认情况下，任何应用（甚至在不同主机上的应用）都可以连接到客户端的转发端口上，但是可以通过指定一个 **_bind address_** 来限制只有指定地址的主机才能连接到转发端口，下面的命令指定只有本机才能连接到转发端口

```zsh
ssh -L 127.0.0.1:80:remote.server:80 jump.server
ssh -L localhost:80:remote.server:80 jump.server
```

## 远程端口转发

在 `OpenSSH` 中，可以使用 `-R` 选项打开远程端口转发

```zsh
ssh -R [bind-address]:<remote-port>:<server-address>:<server-port> remote.server
```

下面的命令在`跳转主机`上创建一个从 remote.server:8080 端口到 http.server:80 端口的转发

```zsh
ssh -R 8080:http.server:80 remote.server
```

将 remote.server 的 8080 端口转发到本地的 80 端口

```zsh
ssh -R 8080:localhost:80 remote.server
```

也可以指定 **_bind address_** 来限制哪些主机可以使用远程端口转发，下面命令指定只有 proxy.server 可以使用 remote.server:8080 端口连接到 http.server:80 端口

```zsh
ssh -R proxy.server:8080:http.server:80 remote.server
```

## 参考文档

[OpenSSH Tunneling](https://en.wikibooks.org/wiki/OpenSSH/Cookbook/Tunnels)