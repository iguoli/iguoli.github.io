---
title: OpenSSH Config
date: 2018-07-31
modify_date: 2020-08-06
tags: OpenSSH
key: OpenSSH-Config-2018-07-31
---

## 客户端配置文件 SSH_CONFIG

参考手册页了解详细内容

```sh
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

```sh
zstyle ':completion:*:ssh:*' hosts off
```

## 服务端配置文件

`Match` 关键字会引入条件块。如果 `Match` 所在行上的所有条件都满足，则该行之后出现的关键字将覆盖配置文件全局部分中设置的关键字，直到另一个 `Match` 行出现或到文件末尾。如果关键字出现在满足条件的多个 `Match` 块中，则第一个满足条件的 `Match` 块中的关键字被应用。

`Match` 的参数是一个或多个条件模式或使用 `ALL` 匹配所有条件。可用的条件是 `User`, `Group`, `Host`, `LocalAddress`, `LocalPort`, `RDomain`, and `Address` (`RDomain` 代表接收连接的 rdomain(4))。多个条件模式由逗号 (`,`) 分隔，并且可以使用 `ssh_config(5)` 的 *PATTERNS* 部分中描述的通配符和否定运算符。

`Address` 条件模式可以包含以 CIDR 地址/掩码格式匹配的地址，例如 `192.0.2.0/24` 或 `2001:db8::/32`。请注意，提供的掩码长度必须与地址一致 - 为地址指定太长的掩码长度或在地址的主机部分中设置了位的掩码长度是错误的。 例如，分别为 192.0.2.0/33 和 192.0.2.0/8。

只能在 `Match` 块中使用关键字的子集。可用的关键字子集是

| Keyword                     | Keyword                         | Keyword                         | Keyword                    | Keyword                  |
| --------------------------- | ------------------------------- | ------------------------------- | -------------------------- | ------------------------ |
| AcceptEnv                   | AllowAgentForwarding            | AllowGroups                     | AllowStreamLocalForwarding | AllowTcpForwarding       |
| AllowUsers                  | AuthenticationMethods           | AuthorizedKeysCommand           | AuthorizedKeysCommandUser  | AuthorizedKeysFile       |
| AuthorizedPrincipalsCommand | AuthorizedPrincipalsCommandUser | AuthorizedPrincipalsFile        | Banner                     | CASignatureAlgorithms    |
| ChrootDirectory             | ClientAliveCountMax             | ClientAliveInterval             | DenyGroups                 | DenyUsers                |
| DisableForwarding           | ExposeAuthInfo                  | ForceCommand                    | GatewayPorts               | GSSAPIAuthentication     |
| HostbasedAcceptedAlgorithms | HostbasedAuthentication         | HostbasedUsesNameFromPacketOnly | IgnoreRhosts               | Include                  |
| IPQoS                       | KbdInteractiveAuthentication    | KerberosAuthentication          | LogLevel                   | MaxAuthTries             |
| MaxSessions                 | PasswordAuthentication          | PermitEmptyPasswords            | PermitListen               | PermitOpen               |
| PermitRootLogin             | PermitTTY                       | PermitTunnel                    | PermitUserRC               | PubkeyAcceptedAlgorithms |
| PubkeyAuthentication        | PubkeyAuthOptions               | RekeyLimit                      | RevokedKeys                | RDomain                  |
| SetEnv                      | StreamLocalBindMask             | StreamLocalBindUnlink           | TrustedUserCAKeys          | X11DisplayOffset         |
| X11Forwarding               | X11UseLocalhost                 |                                 |                            |

## OpenSSH 8.8

- [OpenSSH 8.8 release note](https://www.openssh.com/txt/release-8.8)
- [OpenSSH 8.8 arrives saying goodbye to ssh-rsa support, bug fixes and more](https://www.linuxadictos.com/en/openssh-8-8-arrives-saying-goodbye-to-ssh-rsa-support-bug-fixes-and-more.html)

### 禁止SHA-1算法

> 现版本中，直接默认禁用使用 SHA-1 哈希算法的 RSA 签名。由于 SHA-1 哈希算法已经在密码学上被碰撞破坏（可以以少于5万美刀的成本创建前缀哈希冲突）。
> 对于大多数用户来说，这种变化应该是不可见的，不需要替换 ssh-rsa 密钥。OpenSSH 7.2 版以后，OpenSSH 就已支持 RFC8332 RSA/SHA-256/512 签名，现有的 ssh-rsa 密钥将在可能的情况下自动使用更强的算法。
> 当连接到尚未升级或没有密切跟踪 SSH 协议改进的旧版本时，可能会由于不兼容导致连接失败。对于这些情况，可以手动配置 `HostkeyAlgorithms` 和 `PubkeyAcceptedAlgorithms` 选项启用 RSA/SHA1 进行连接和用户身份验证。

可以在用户配置文件 `~/.ssh/config` 中对以下单台主机启用 RSA/SHA1：

```ssh
Host old-host
    HostkeyAlgorithms +ssh-rsa
    PubkeyAcceptedAlgorithms +ssh-rsa
```

## SSH Client端个人配置文件

```sh
vim ~/.ssh/config
```

```text
# The settings for all hosts
# Keep the ssh connection alive
ServerAliveInterval 30
ServerAliveCountMax 10

# Since OpenSSH 8.8, sha-1 (ssh-rsa) hash algorithm was disabled by default
# Set below options to enable it with caution
HostKeyAlgorithms +ssh-rsa
PubkeyAcceptedKeyTypes +ssh-rsa

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

```sh
ssh -J user@jumphost1:22 fred@192.168.5.38
```

在 OpenSSH 7.2 及之前的版本，只能在配置文件或命令行使用`ProxyCommand`选项

```text
Host server
    ProxyCommand ssh -W %h:%p jumphost
```

对应的命令行方式是`-o ProxyCommand`参数

```sh
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
[sshd_config(5) — Linux manual page](https://man7.org/linux/man-pages/man5/sshd_config.5.html)
[SSH Jump Host](https://wiki.gentoo.org/wiki/SSH_jump_host)

[主机跳转]: https://en.wikibooks.org/wiki/OpenSSH/Cookbook/Proxies_and_Jump_Hosts 
[多主机跳转]: https://en.wikibooks.org/wiki/OpenSSH/Cookbook/Proxies_and_Jump_Hosts#Old:_Recursively_Chaining_Gateways_Using_stdio_Forwarding