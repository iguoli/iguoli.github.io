---
title: Linux Commands Summary
date: 2017-12-03
modify_date: 2021-11-09
tags: Linux Utils
key: Linux-Commands-2017-12-03
---

## [Linux 在线手册][00]

- [1: User commands][01]; man-pages includes a very few Section 1 pages that document programs supplied by the GNU C library.
- [2: System calls][02] documents the [system calls] provided by the Linux kernel.
- [3: Library functions][03] documents the functions provided by the standard C library.
- [4: Devices][04] documents details of various devices, most of which reside in `/dev`.
- [5: Files][05] describes various file formats, and includes [proc(5)], which documents the `/proc` file system.
- [7: Overviews, conventions, and miscellaneous.][07]
- [8: Superuser and system administration commands][08]; man-pages includes a very few Section 8 pages that document programs supplied by the GNU C library.

<!--more-->

[Online man pages][09]

[linux命令在线中文手册][10]

[00]: https://www.kernel.org/doc/man-pages/
[01]: http://man7.org/linux/man-pages/dir_section_1.html
[02]: http://man7.org/linux/man-pages/dir_section_2.html
[system calls]: http://man7.org/linux/man-pages/man2/syscalls.2.html
[03]: http://man7.org/linux/man-pages/dir_section_3.html
[04]: http://man7.org/linux/man-pages/dir_section_4.html
[05]: http://man7.org/linux/man-pages/dir_section_5.html
[proc(5)]: http://man7.org/linux/man-pages/man5/proc.5.html
[07]: http://man7.org/linux/man-pages/dir_section_7.html
[08]: http://man7.org/linux/man-pages/dir_section_8.html
[09]: http://man7.org/linux/man-pages/index.html
[10]: http://linux.51yip.com

## sort 排序

[GNU coreutils 7.5](http://article.gmane.org/gmane.comp.gnu.core-utils.announce/52) 在2009年8月发布以后，`sort` 提供了 `-h` 参数，可以对类似 `du -h` 等命令产生的带有数字后辍的输出进行排序

[How can I sort du -h output by size](https://serverfault.com/questions/62411/how-can-i-sort-du-h-output-by-size)

```bash
du -sh * | sort -h
```

对数字排序

```bash
sort -n
```

对类似于 `abc:10:def` 的文本，可以通过 `-t` 参数设定分隔符，使用 `-k` 参数来选择分隔后的列

```bash
cat /etc/passwd | sort -n -t':' -k3
```

## Start an application detached

参考 [How to cleanly launch a GUI app via the Terminal?](https://askubuntu.com/questions/10547/how-to-cleanly-launch-a-gui-app-via-the-terminal)

如果是 bash 或 zsh，可以在使用 `&!`，比如在命令行打开 `gvim`

```bash
gvim &!
```

## sudo and su

```bash
sudo -H -u postgres psql db

su - postgres -c 'psql db'
```

## 使用 Type 命令查看系统命令

`-t` 选项，打印所给命令的类型

- alias (command is shell alias)
- keyword (command is shell reserved word)
- function (command is shell function)
- builtin (command is shell builtin)
- file (command is disk file)

```bash
$ type -t cd
builtin
```

`-p` 选项，打印所给命令的文件路径，只对外部命令有效

```bash
$ type -p date
/bin/date
```

`-a` 选项，打印所给命令的所有信息，包含命令类型，文件路径等

```bash
$ type -a ls
ls is aliased to `ls --color=auto'
ls is /bin/ls
```

## 使用 STAT 查看文件或文件系统状态

查看文件系统状态

```bash
stat -f /home
```

查看文件状态

```bash
find /var/log/ -name '*.log' -type f | xargs stat --printf='%s\t%n\n'
```

## 网络相关命令

```bash
# 通过主机域名查看别名或IP
nslookup www.example.com

# 通过IP查询主机域名
nslookup 10.2.154.201

# 查看本机FQDN主机名
hostname -f

# 查看本机ip
hostname -i
```

## 文件比较(diff)

参考阮一峰的[读懂diff](http://www.ruanyifeng.com/blog/2012/08/how_to_read_diff.html)

unified 格式  
`-u`、`-U NUM` 或 `--unified[=NUM]`

```bash
# 默认显示3行上下文
diff -u f1 f2

# 显示2行上下文
diff -U 2 f1 f2
```

```text
--- f1 2012-08-29 16:45:41.000000000 +0800
+++ f2 2012-08-29 16:45:51.000000000 +0800
@@ -1,7 +1,7 @@
 a
 a
 a
-a
+b
 a
 a
 a
```

第一部分，是文件的基本信息。

```text
--- f1 2012-08-29 16:45:41.000000000 +0800
+++ f2 2012-08-29 16:45:51.000000000 +0800
```

`---`表示变动前的文件，`+++`表示变动后的文件。

第二部分，变动的位置用两个@作为起首和结束。

```text
@@ -1,7 +1,7 @@
```

前面的`-1,7`分成三个部分：减号`-`表示第一个文件(f1)，`1`表示第1行，`7`表示连续7行。合在一起，就表示下面是第一个文件从第1行开始的连续7行。同样的，`+1,7`表示变动后，成为第二个文件从第1行开始的连续7行。

第三部分，是变动的具体内容。

```text
 a
 a
 a
-a
+b
 a
 a
 a
```

除了有变动的那些行以外，也是上下文各显示3行。它将两个文件的上下文，合并显示在一起，所以叫做"合并格式"。每一行最前面的标志位，

- 空表示无变动
- 减号`-`表示**第一个文件**删除的行
- 加号`+`表示**第二个文件**新增的行。

## Linux下判断文件的编码

1. 使用`enca`命令可以直接查看文件的字符编码
2. 使用`file`命令也可以查看文件的字符编码，`file -i`会输出MIME-type格式的信息

## Linux下文件批量重命名

### 使用`rename`命令

```bash
$ rename -n -s markdown md *.markdown

'New-Linux-Setup.markdown' would be renamed to 'New-Linux-Setup.md'
'Vim-Tips.markdown' would be renamed to 'Vim-Tips.md'
```

其中，**-n** 表示 **--just-print/--dry-run**，**-s** 表示 **--subst from to**

### 使用`ls -1`和`sed`命令

```bash
ls -1 | sed "s/被替换字符\(保留字符\)/mv & 新字符\1/" | sh -v
```

## 使用 dd 生成文件

在当前目录下会生成一个100M的test文件，文件内容为全0（因从/dev/zero中读取，/dev/zero为0源）

```bash
dd if=/dev/zero of=test bs=1M count=100
```

## 配置 sudo 用户

### 语法

```text
USERS HOSTS=(RUNAS) [NOPASSWD:]COMMANDS
```

### 配置无密码用户

让 `cloudera` 用户和所有属于 `wheel` 用户组的用户可以无密码运行任何命令

```bash
$ sudo vim /etc/sudoers.d/cloudera

cloudera ALL=(ALL) NOPASSWD:ALL
%wheel ALL=(ALL) NOPASSWD:ALL
```

查看 `man sudoers` 的 `EXAMPLE` 章节，学习更多例子

## Linux 下查看 cpu 个数及使用率

```bash
$ lscpu

$ top
按数字1显示所有cpu信息

$ htop
```

## Linux下显示系统版本

### Ubuntu

```bash
lsb_release

hostnamectl
```

### Redhat or CentOS

```bash
hostnamectl

cat /etc/issue

# Centos
cat /etc/centos-release

# Redhat
cat /etc/redhat-release
```

## Linux 下查看/添加/删除/修改用户

使用 `lid` 或 `libuser-lid` (较新的系统) 命令查看用户都在哪些组，或某个组包含哪些用户

```bash
# Ubuntu 系统安装 lid 或 libuser-lid
sudo apt install -y libuser

# 查看用户属于哪些组
lid [username]

# 查看组 wheel 中有哪些用户
lid -g wheel
```

使用 `useradd` 命令添加用户

```bash
# -m 生成用户目录，-G 添加用户到额外组
useradd -m -G wheel,docker login-name
passwd login-name
```

使用 `usermod` 命令修改用户

```bash
# 将用户添加到docker
usermod -aG docker login-name

# 修改用户名
usermod -l new-name old-name
mv /home/old-name /home/new-name
usermod -d /home/new-name new-name
usermod -c “description” new-name
groupmod -n new-group old-group
```

使用 `userdel` 删除用户

```bash
# -r 会连同用户目录一起删除
userdel -r login-name

```
