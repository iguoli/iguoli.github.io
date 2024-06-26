---
title: Linux Commands Summary
date: 2017-12-03
modify_date: 2022-05-30
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

## 删除变量中所有的空格，换行符，制表符等

```sh
echo $a
```

```
 1
2   3
4

```

使用 `tr` 命令

```sh
echo $a | tr -d '[:space:]' # output 1234%
```

使用 `bash parameter expansion`

| Parameter Expansion            | Description                                                |
| ------------------------------ | ---------------------------------------------------------- |
| `${parameter/pattern/string}`  | only the first match is replaced.                          |
| `${parameter//pattern/string}` | all matches of pattern are replaced with string.           |
| `${parameter/#pattern/string}` | match at the beginning of the expanded value of parameter. |
| `${parameter/%pattern/string}` | match at the end of the expanded value of parameter.       |

If string is *null*, matches of pattern are deleted and the `/` following pattern may be omitted. 

```sh
echo ${a//[[:space:]]/} # output 1234

# '/' may be omitted if string is null 
echo ${a//[[:space:]]} # output 1234
```

## cut

去掉一串数字的最后3个字符, `-c, --characters` 表示按字符选择，`-4` 表示选择从第一个字符到倒数第四个字符，也就是不包含最后三个字符。这个命令可以用于去掉 timestamp 中的最后3个表示纳秒的字符。 

```sh
# 输出 "1234"
echo 1234567 | cut -c -4
```

## less

`-p` 选项，使用 search pattern 打开文件

```sh
less -pPattern file
```

`-s` 选项，打开文件时删除重复的空行

```sh
less -s file
```

`-N` 选项，显示行号

```sh
less -N file
```

`+F` (forward) 选项是 less 中的实时监控模式。`+F` 选项使 less 显示实时添加到文件中的最新消息或行。`+` 标志表示将选项视为在 less 中使用。如果文件已经在 less 中打开，请按 <kbd>F</kbd> 键进入前进模式。

> F, Scroll  forward, and keep trying to read when the end of file is reached.  Normally this command would be used when already at the end of the file.  It is a way to monitor the tail of a file which is growing while it is being viewed.  (The behavior is similar  to  the  "tail -f" command.)  To stop waiting for more data, enter the interrupt character (usually ^C).  On some systems you can also use ^X.

```sh
less +F /var/log/syslog

sudo dmesg | less +F
```

在 less 中还可以像 vim 一样标记位置，使用 <kbd>m</kbd> 键标记位置，使用 <kbd>'</kbd> 键恢复位置。同时，在 less 中还可以使用 <kbd>v</kbd> 键打开系统默认的编辑器。

## dig

使用 `@` 符号来指定要查询的 DNS 服务器，如果不指定 DNS 服务器，`dig` 会依次使用 `/etc/resolv.conf` 中的地址作为 DNS 服务器

```sh
dig www.youtube.com @1.1.1.1
```

## paste

`paste` 命令会将多个输入文件的对应行合并成一行，就像函数式编程中的 zip 函数一样，例如：

num.txt

```text
1
2
3
```

char.txt

```text
a
b
c
```

```sh
paste num.txt char.txt
```

输出

```text
1   a
2   b
3   c
```

### 参数

- `-s`: 将从输入中读取到的所有行合并为一行，如果指定多个输入，则每个输入各自一行
- `-d`: 指定合并时使用的分隔符

```sh
paste -s nub.txt char.txt
```

输出

```text
1 2 3
a b c
```

### 例子

用法1：将当前目录下的文件名按三列展示

```sh
ls | paste - - -
```

用法2：将单个文件的多行合并为一行

```sh
paste -s myfile
```

用法3：生成类似 PATH 环境变量的目录列表

```sh
find / -name bin -type d | paste -s -d: -
```

用法4：给输入中的每一行加上行号

```sh
sed = char.txt | paste - -
```

这相当于使用命令 `nl char.txt`

## Bash for loop

```sh
#!/bin/bash
for i in 1 2 3 4 5
do
   echo "Welcome $i times"
done
```

```sh
for output in $(Linux Command)
do
    command1 on $output
    command2 on $output
    commandN
done
```

## sort 排序

### 对带数字后辍的输出排序

[GNU coreutils 7.5](http://article.gmane.org/gmane.comp.gnu.core-utils.announce/52) 在2009年8月发布以后，`sort` 提供了 `-h` 参数，可以对类似 `du -h` 等命令产生的带有数字后辍的输出进行排序

[How can I sort du -h output by size](https://serverfault.com/questions/62411/how-can-i-sort-du-h-output-by-size)

```sh
du -sh * | sort -h
```

### 对数字排序

```sh
sort -n
```

### 对多列文本中的某列排序

对类似于 `abc:10:def` 的文本，可以通过 `-t` 参数设定分隔符，使用 `-k` 参数来选择分隔后的列

```sh
cat /etc/passwd | sort -n -t':' -k3
```

## uniq 去重和查重

`uniq` 工具会检查文本中相邻行是否重复，不使用任何参数的话，会将相邻的重复行删除，只保留第一行。 因为 `uniq` 工具只能检查相邻行是否重复，因此在使用 `uniq` 命令前需要使用 `sort` 命令对文本排序，也可以直接使用 `sort -u` 命令对文本去重。

### 去重

```sh
sort abc.txt | uniq

sort -u abc.txt
```

### 查重

```sh
# 打印所有行，在每行前显示重复个数
sort abc.txt | uniq -c

# 只显示有重复的行和重复个数
sort abc.txt | uniq -cd
```

## Start an application detached

参考 [How to cleanly launch a GUI app via the Terminal?](https://askubuntu.com/questions/10547/how-to-cleanly-launch-a-gui-app-via-the-terminal)

如果是 bash 或 zsh，可以在使用 `&!`，比如在命令行打开 `gvim`

```sh
gvim &!
```

## sudo and su

切换到 `postgres` 用户。

```sh
sudo -iu postgres

su - postgres
```

使用 `postgres` 用户执行命令

```sh
sudo -u postgres psql db

su - postgres -c 'psql db'
```

## 使用 Type 命令查看系统命令

`-t` 选项，打印所给命令的类型

- alias (command is shell alias)
- keyword (command is shell reserved word)
- function (command is shell function)
- builtin (command is shell builtin)
- file (command is disk file)

```sh
$ type -t cd
builtin
```

`-p` 选项，打印所给命令的文件路径，只对外部命令有效

```sh
$ type -p date
/bin/date
```

`-a` 选项，打印所给命令的所有信息，包含命令类型，文件路径等

```sh
$ type -a ls
ls is aliased to `ls --color=auto'
ls is /bin/ls
```

## 使用 STAT 查看文件或文件系统状态

查看文件系统状态

```sh
stat -f /home
```

查看文件状态

```sh
find /var/log/ -name '*.log' -type f | xargs stat --printf='%s\t%n\n'
```

## 网络相关命令

```sh
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

```sh
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

```sh
$ rename -n -s markdown md *.markdown

'New-Linux-Setup.markdown' would be renamed to 'New-Linux-Setup.md'
'Vim-Tips.markdown' would be renamed to 'Vim-Tips.md'
```

其中，**-n** 表示 **--just-print/--dry-run**，**-s** 表示 **--subst from to**

### 使用`ls -1`和`sed`命令

```sh
ls -1 | sed "s/被替换字符\(保留字符\)/mv & 新字符\1/" | sh -v
```

## 使用 dd 生成文件

在当前目录下会生成一个100M的test文件，文件内容为全0（因从/dev/zero中读取，/dev/zero为0源）

```sh
dd if=/dev/zero of=test bs=1M count=100
```

## Linux 下查看 cpu 个数及使用率

```sh
$ lscpu

$ top
按数字1显示所有cpu信息

$ htop
```
