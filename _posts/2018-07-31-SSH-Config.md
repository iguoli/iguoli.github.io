---
title: SSH Config 配置说明
date: 2018-07-31
modify_date: 2019-12-10
tags: ssh ssh_config
key: 2018-07-31-SSH-Config
---

## SSH_CONFIG 中的选项说明

```zsh
man 5 ssh_config
```

## SSH Client端个人配置文件

```zsh
vim ~/.ssh/config
```

```txt
# For all hosts
# Keep the ssh connection alive
ServerAliveInterval 30
ServerAliveCountMax 10

# For individual host
Host github github.com
    HostName github.com
    Port 22
    User git
    IdentityFile ~/.ssh/li_rsa
    ProxyJump user@jumphost.example.org:22
```

## 主机跳转

从 OpenSSH 7.3 版本开始，最简单的方法是在配置文件里使用`ProxyJump`指令

```txt
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

```txt
Host server
    Hostname server.example.org
    ProxyCommand ssh -W %h:%p jumphost.example.org
```

对应的命令行方式是`-o ProxyCommand`参数

```zsh
ssh -o ProxyCommand="ssh -W %h:%p jumphost.example.org" server.example.org
```

如果跳转机与目标机的用户名及密钥不一样，可以在配置文件中分别指定

```txt
Host server
    HostName server.example.org
    User fred
    IdentityFile /home/fred/.ssh/fred_key
    ProxyCommand ssh -l user -i /home/fred/.ssh/user_key jumphost.example.org -W %h:%p
```

## 参考文档

[OpenSSH Proxies and Jump Hosts](https://en.wikibooks.org/wiki/OpenSSH/Cookbook/Proxies_and_Jump_Hosts)