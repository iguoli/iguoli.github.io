---
title: SSH Config
date: 2018-07-31
modify_date: 2020-06-05
tags: SSH
key: SSH-Config-2018-07-31
---

## SSH_CONFIG 中的选项说明

```zsh
man 5 ssh_config
```

<!--more-->

## SSH Client端个人配置文件

```zsh
vim ~/.ssh/config
```

```text
# The settings for all hosts
# Keep the ssh connection alive
ServerAliveInterval 30
ServerAliveCountMax 10

# Access the remote host using proxy
Host github github.com
    HostName github.com
    Port 22
    User git
    IdentityFile ~/.ssh/id_rsa
    # Use http proxy
    ProxyCommand connect -H 127.0.0.1:7890 %h %p
    # Use socks5 proxy
    ProxyCommand connect -S 127.0.0.1:7891 %h %p

# Access the remote host using a straightforward chain
# e.g. access the host3 through host1 and host2
# Usage: ssh host3
Host host1
    HostName host1
    User username1
    IdentityFile ~/.ssh/host1_key

Host host2
    HostName host2
    User username2
    IdentityFile ~/.ssh/host2_key
    # OpenSSH version 7.3 and later
    ProxyJump host1

Host host3
    HostName host3
    User username3
    IdentityFile ~/.ssh/host3_key
    # OpenSSH version 7.2 and earlier
    ProxyCommand ssh -W %h:%p host2

# Local port forwarding
# e.g. ssh -N -f -L 5432:dbserver:5432 username@jumphost
# Usage: ssh -N -f jumphost
Host jumphost
    Hostname jumphost
    User username
    IdentityFile ~/.ssh/id_rsa
    LocalForward 5432 dbserver:5342
```

## [主机跳转]

从 OpenSSH 7.3 版本开始，最简单的方法是在配置文件里使用`ProxyJump`指令

```text
Host server
    HostName 192.168.88.12
    User fred
    ProxyJump user@jumphost.example.org:22
```

对应的命令行方式是`-J`参数

```zsh
ssh -J user@jumphost1.example.org:22 fred@192.168.5.38
```

在 OpenSSH 7.2 及之前的版本，只能在配置文件或命令行使用`ProxyCommand`选项

```text
Host server
    Hostname server.example.org
    ProxyCommand ssh -W %h:%p jumphost.example.org
```

对应的命令行方式是`-o ProxyCommand`参数

```zsh
ssh -o ProxyCommand="ssh -W %h:%p jumphost.example.org" server.example.org
```

如果跳转机与目标机的用户名及密钥不一样，可以在配置文件中分别指定

```text
Host server
    HostName server.example.org
    User fred
    IdentityFile /home/fred/.ssh/fred_key
    ProxyCommand ssh -l user -i /home/fred/.ssh/user_key jumphost.example.org -W %h:%p
```

### [多主机跳转]

这里三个主机链接在一起，最后面的主机为 `machine3`，在这条链中的任何主机都可以直接访问。因此，可以通过 `ssh machine3` 直接访问主机 `machine3`。

```text
Host machine1
        Hostname server.example.org
        User fred
        IdentityFile /home/fred/.ssh/machine1_e25519
        Port 2222

Host machine2
        Hostname 192.168.15.21
        User fred
        IdentityFile /home/fred/.ssh/machine2_e25519
        Port 2222
        ProxyCommand ssh -W %h:%p machine1

Host machine3
        Hostname 10.42.0.144
        User fred
        IdentityFile /home/fred/.ssh/machine3_e25519
        Port 2222
        ProxyCommand ssh -W %h:%p machine2
```

如果 `ProxyJump` 选项可用，可以使用命令 `ssh -J user1@host1,user2@host2 user3@host3` 在命令行指定多主机跳转，外个跳转主机用逗号分隔。

## 多主机跳转端口转发

端口转发也可以使用多主机跳转的配置。因此，可以使用命令 `ssh -N -L 8080:localhost:80 machine3` 直接将本地 `8080` 端口转发到 `machine3` 的 `80` 端口。

也可以使用 `LocalForward` 选项将本地端口转发的配置写到 `~/.ssh/config` 文件中。

```text
Host port-5432
    Hostname dbserver
    User dba
    IdentityFile ~/.ssh/id_rsa
    ProxyJump machine3
    LocalForward 5432 localhost:5342
```

通过上面配置，可以使用命令 `ssh -N port-5432` 建立本地 `5342` 到 `dbserver:5432` 的端口转发（经过多次跳转）。

## 参考文档

[OpenSSH](https://en.wikibooks.org/wiki/OpenSSH)
[SSH Jump Host](https://wiki.gentoo.org/wiki/SSH_jump_host)

[主机跳转]: https://en.wikibooks.org/wiki/OpenSSH/Cookbook/Proxies_and_Jump_Hosts 
[多主机跳转]: https://en.wikibooks.org/wiki/OpenSSH/Cookbook/Proxies_and_Jump_Hosts#Old:_Recursively_Chaining_Gateways_Using_stdio_Forwarding