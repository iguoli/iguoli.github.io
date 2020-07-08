---
title: SSH Port Forwarding
date: 2018-09-08
modify_date: 2020-07-08
tags: SSH
key: SSH-Port-Forwarding-2018-09-08
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

<!--more-->

`-C` 选项可以对所有转发数据进行压缩，如果转发数据大部分是文本数据，会提高转发效率，如果是二进制数据，则会降低转发效率。

## [常规隧道][Tunneling]（本地端口转发）

在常规端口转发中，连接到本地端口将会被转发到远程主机端口。这可以保护那些非安全的连接协议，或使远程服务看起来像本机服务。

```zsh
ssh -L [bind-address]:<local-port>:<remotehost>:<remote-port> <jumphost>
```

将本地 8080 端口转发到远程主机 80 端口

```zsh
ssh -L 8080:localhost:80 remotehost
```

也可以通过中间主机 `jumphost` 跳转，将本地端口通过中间主机转发到远程主机

```zsh
ssh -L 8080:remotehost:80 jumphost
```

默认情况下，任何应用（甚至在不同主机上的应用）都可以连接到客户端的转发端口上，但是可以通过指定一个 **bind address** 来限制只有指定地址的主机才能连接到转发端口，下面的命令指定只有本机才能连接到转发端口

```zsh
ssh -L 127.0.0.1:80:remotehost:80 jumphost
```

## [反向隧道][Reverse Tunneling]（远程端口转发）

反向隧道与常规隧道的方向相反，在反向隧道中，远程主机端口被转发到本地主机端口，一旦连接建立，其行为与常规隧道一致，连接到本地主机的目标端口同样会被转发到远程主机上的端口。

```zsh
ssh -R [bind-address]:<remote-port>:<server-address>:<server-port> remotehost
```

将远程主机 8080 端口转发到本地 80 端口

```zsh
ssh -R 8080:localhost:80 remotehost
```

在**中间主机**上创建一个从**远程主机** (`remotehost`) 到**远程主机** (`http.server`) 的端口转发

```zsh
ssh -R 8080:http.server:80 remotehost
```

也可以指定 **bind address** 来限制哪些主机可以使用远程端口转发。下面命令指定只有 `proxy.server` 可以使用远程端口转发。

```zsh
ssh -R proxy.server:8080:http.server:80 remotehost
```

## [SOCKS 代理][SOCKS Proxy]

本地端口转发和远程端口转发只能转发单一端口，SOCKS 代理可以动态转发所有端口，无需指定远程主机端口。

```zsh
ssh -D 1080 remotehost
```

在浏览器中设置 socks5 代理为 127.0.0.1:1080，即可在浏览器中将所有端口动态转发到远程主机。

如果需要使用中间主机跳转，只需使用 `ProxyJump` 选项指定中间主机即可

```zsh
ssh -D 8443 -J jumphost remotehost
```

## [多主机端口转发]

如果 `ProxyJump` 选项可用，可以创建一个通过 `jumphost` 从 `localhost:8080` 到 `machine2:80` 的隧道。

```zsh
ssh -L 8080:localhost:80 -J jumphost machine2
```

`ProxyJump` 选项也可以将多台中间主机通过逗号分隔，组成转发链。

```zsh
ssh -L 8080:localhost:80 -J jumphost1,jumphost2 machine2
```

如果在 ssh_config 文件中配置好 `ProxyJump` 选项，可以直接使用本地端口转发，跳转会自动完成。

```zsh
ssh -L 8080:localhost:80 machine2
```

## [在已建立的连接中添加或删除隧道]

可以使用转义字符（`~`）在已建立的 SSH 连接上添加或删除常规隧道、反向隧道或 SOCKS 代理。可以查看 [ssh(1)] 手册页了解关于转义字符的内容。

使用转义字符在 SSH 会话上添加常规隧道

```zsh
~C
L 8080:localhost:80
```

从 SSH 会话上删除常规隧道

```zsh
~C
KL 8080
```

添加常规隧道，反向隧道和 SOCKS 代理分别对应：`-L`，`-R`，`-D`。删除隧道分别对应：`KL`，`KR`，`KD` + `端口号`。

## 参考文档

[OpenSSH Tunneling](https://en.wikibooks.org/wiki/OpenSSH/Cookbook/Tunnels)

[Tunneling]: https://en.wikibooks.org/wiki/OpenSSH/Cookbook/Tunnels#Tunneling
[Reverse Tunneling]: https://en.wikibooks.org/wiki/OpenSSH/Cookbook/Tunnels#Reverse_Tunneling
[SOCKS Proxy]: https://en.wikibooks.org/wiki/OpenSSH/Cookbook/Proxies_and_jumphosts#SOCKS_Proxy
[多主机端口转发]: https://en.wikibooks.org/wiki/OpenSSH/Cookbook/Proxies_and_jumphosts#Port_Forwarding_Through_One_or_More_Intermediate_Hosts
[在已建立的连接中添加或删除隧道]: https://en.wikibooks.org/wiki/OpenSSH/Cookbook/Tunnels#Adding_or_Removing_Tunnels_within_an_Established_Connection
[ssh(1)]: https://man.openbsd.org/ssh