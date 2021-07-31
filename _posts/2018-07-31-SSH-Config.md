---
title: SSH Config
date: 2018-07-31
modify_date: 2020-08-06
tags: SSH
key: SSH-Config-2018-07-31
---

## 客户端配置文件 SSH_CONFIG

参考手册页了解详细内容

```bash
man 5 ssh_config
```

### 配置文件段

客户端配置文件可以划分成段，每个段包含一个或一组主机的设置。OpenSSH 以 `Host` 关键字开始一个新段，后面跟的字符串称为**主机规范 (Host Specification)**。主机规范可以是以下几种类型：

<!--more-->

#### 主机名

```text
Host server.example.com
```

#### IP 地址

```text
Host 10.10.10.10
```

#### 主机别名

```text
Host nickname
```

#### glob_expression 模式

```text
Host *.example.com
Host 10.10.10.*
Host *
```

可以在主机名前加 **感叹号** `!` 忽略某些主机，例如

```text
Host * !jumphost
    ProxyJump jumphost
```

会对所有主机添加 `ProxyJump` 指令，但会忽略 `jumphost` 主机。

### 多次匹配

因为主机规范中可以使用模式，因此一个主机名可以和配置文件中一或多个段匹配。多个段中的关键字可以同时生效，如果同一个关键字在多个段中出现，OpenSSH 将使用第一个出现的值。例如：

```text
Host *.example.com
    Compression yes
    ForwardAgent yes

Host *.com
    Compression no
    PasswordPromptLogin no
```

如果执行 `ssh server.example.com`，使用的配置会是

```text
Compression yes
ForwardAgent yes
PasswordPromptLogin no
```

如果某些关键字可以应用于所有主机，可以使用下面的段来配置

```text
Host *
```

该段如果放在文件第一段，会强制所有主机应用配置，比如禁用某些非安全选项。如果放在文件最后一段，那么可以做为所有主机缺省配置，当主机没有相应设置时，默认开启某些特性。

oh-my-zsh 对 ssh 主机名的自动补全有时候会不正常，可以执行以下命令来解决
{:.warning}

```bash
zstyle ':completion:*:ssh:*' hosts off
```

## SSH Client端个人配置文件

```bash
vim ~/.ssh/config
```

```text
# The settings for all hosts
# Keep the ssh connection alive
ServerAliveInterval 30
ServerAliveCountMax 10

# Access the remote host using proxy
Host github.com
    User git
    Port 22
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
# e.g. ssh -N -f -J user@jumphost -L 5432:localhost:5432 dba@dbserver
# Usage: ssh -N -f forward-5432
Host forward-5432
    Hostname dbserver
    User dba
    IdentityFile ~/.ssh/id_rsa
    ProxyJump user@jumphost
    LocalForward 5432 localhost:5342
```

## [主机跳转]

从 OpenSSH 7.3 版本开始，最简单的方法是在配置文件里使用`ProxyJump`指令

```text
Host server
    HostName 192.168.88.12
    User fred
    ProxyJump user@jumphost:22
```

对应的命令行方式是`-J`参数

```bash
ssh -J user@jumphost1:22 fred@192.168.5.38
```

在 OpenSSH 7.2 及之前的版本，只能在配置文件或命令行使用`ProxyCommand`选项

```text
Host server
    ProxyCommand ssh -W %h:%p jumphost
```

对应的命令行方式是`-o ProxyCommand`参数

```bash
ssh -o ProxyCommand="ssh -W %h:%p jumphost" server
```

如果跳转机与目标机的用户名及密钥不一样，可以在配置文件中分别指定

```text
Host server
    User fred
    IdentityFile /home/fred/.ssh/fred_key
    ProxyCommand ssh -l user -i /home/fred/.ssh/user_key jumphost -W %h:%p
```

### [多主机跳转]

这里三个主机链接在一起，最后面的主机为 `machine3`，在这条链中的任何主机都可以直接访问。因此，可以通过 `ssh machine3` 直接访问主机 `machine3`。

```text
Host machine1
        User fred
        IdentityFile /home/fred/.ssh/machine1_e25519
        Port 2222

Host machine2
        User fred
        IdentityFile /home/fred/.ssh/machine2_e25519
        Port 2222
        ProxyJump machine1

Host machine3
        User fred
        IdentityFile /home/fred/.ssh/machine3_e25519
        Port 2222
        ProxyJump machine2
```

如果 `ProxyJump` 选项可用，可以使用命令 `ssh -J user1@host1,user2@host2 user3@host3` 在命令行指定多主机跳转，多个跳转主机之间用逗号分隔。

## 多主机跳转端口转发

端口转发也可以使用多主机跳转的配置。因此，可以使用命令 `ssh -N -L 8080:localhost:80 machine3` 直接将本地 `8080` 端口转发到 `machine3` 的 `80` 端口。

也可以使用 `LocalForward` 选项将本地端口转发的配置写到 `~/.ssh/config` 文件中。

```text
Host forward-5432
    Hostname dbserver
    User dba
    IdentityFile ~/.ssh/id_rsa
    ProxyJump machine3
    LocalForward 5432 localhost:5342
```

通过上面配置，可以使用命令 `ssh -N forward-5432` 建立本地 `5342` 到 `dbserver:5432` 的端口转发（经过多次跳转）。

## 参考文档

[OpenSSH](https://en.wikibooks.org/wiki/OpenSSH)
[SSH Jump Host](https://wiki.gentoo.org/wiki/SSH_jump_host)

[主机跳转]: https://en.wikibooks.org/wiki/OpenSSH/Cookbook/Proxies_and_Jump_Hosts 
[多主机跳转]: https://en.wikibooks.org/wiki/OpenSSH/Cookbook/Proxies_and_Jump_Hosts#Old:_Recursively_Chaining_Gateways_Using_stdio_Forwarding