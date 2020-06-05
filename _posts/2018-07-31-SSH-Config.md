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
# e.g. ssh -N -f -L :5432:db-server:5432 username@jump-host
# Usage: ssh -N -f jump-host
Host jump-host
    Hostname jump-host
    User username
    IdentityFile ~/.ssh/id_rsa
    LocalForward 5432 db-server:5342
```

## 主机跳转

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

## 参考文档

[OpenSSH Proxies and Jump Hosts](https://en.wikibooks.org/wiki/OpenSSH/Cookbook/Proxies_and_Jump_Hosts)