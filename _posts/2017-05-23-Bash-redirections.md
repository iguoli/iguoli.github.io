---
title: Bash Redirections
date: 2017-05-23
modify_date: 2019-12-17
tags: Bash
key: Bash-Redirections-2017-05-23
---

## Bash 重定向

当 Bash 启动的时候，它会打开三个标准文件描述符 (File Descriptor)

- `/dev/stdin` - file descriptor 0
- `/dev/stdout` - file descriptor 1
- `/dev/stderr` - file descriptor 2

除非你将文件描述符关了，否则它总是指向某个文件。通常 Bash 启动的这三个文件描述符，`stdin`, `stdout` 和 `stderr` 会指向你的终端。从终端读取输入，也输出到终端。

<!--more-->

假设你的终端是 `/dev/tty0`，那么当 Bash 启动的时候，你的文件描述符表会像下面这样

![initial-fd-table](https://catonmat.net/images/bash-redirections/initial-fd-table.png)

当 Bash 运行一个命令时，它会 folk 出一个子进程 (查看 `man 2 fork`)，继承父进程的所有文件描述符，然后设置重定向，最后执行该命令 (查看 `man 3 exec`)。

### 将标准输出和标准错误重定向到相同文件

`2>1` 表示将 `stderr` 重定向到文件名为 `1` 的文件。正确的将 `stderr` 重定向到 `stdout` 的写法是 `2>&1`，使用 `&` 符号来表示后面跟的是一个文件描述符，而不是一个文件名。

下面的命令将 `stdout` 和 `stderr` 都重定向到文件 `file`

```sh
command >file 2>&1
```

当 Bash 看到重定向时，会按照从左到右的顺序来处理，过程如下：

首先是初始化的文件描述符表  
![redirections1-1](https://catonmat.net/images/bash-redirections/initial-fd-table.png)
 
然后 Bash 处理第一个重定向 `>file`，让 `stdout` 指向 `file`  
![redirections1-2](https://catonmat.net/images/bash-redirections/redirect-stdout.png)

接着 Bash 处理第二个重定向 `2>&1`，让文件描述符2指向与文件描述符1相同的地方  
![redirections1-3](https://catonmat.net/images/bash-redirections/redirect-stdout-stderr.png)

Bash 使用 `&>` 操作符简化上面的重定向命令

```sh
command &>file
```

注意，重定向的顺序非常重要，下面的重定向只将 `stdout` 重定向到了文件 `file`，而 `stderr` 依然还是指向到原来的 terminal。

```sh
command 2>&1 >file
```
执行该命令时的过程如下：

初始的文件描述符表  
![redirections2-1](https://catonmat.net/images/bash-redirections/initial-fd-table.png)

然后处理 `2>&1`  
![redirections2-2](https://catonmat.net/images/bash-redirections/duplicate-stderr-stdout.png)

接着处理 `>file`，`stdout` 被重定向到 `file`，但 `stderr` 没变，还是指向原来的终端  
![redirections2-3](https://catonmat.net/images/bash-redirections/duplicate-stderr-stdout-stdout-file.png)

### 忽略命令产生的输出

```sh
command > /dev/null
```

`/dev/null` 是个特殊的文件，任何写入到它的数据都会被忽略。所以将标准输出重定向到这个特殊文件来忽略命令产生的输出。同样，我们也可以同时将标准输出和标准错误重定向到这个文件。

```sh
command >/dev/null 2>&1
```

或者使用简单的写法

```sh
command &> /dev/null
```

这时的文件描述符表如下  
![redirect-stdout-stdout-dev-null](https://catonmat.net/images/bash-redirections/redirect-stdout-stderr-dev-null.png)

## 参考链接

[All about redirections](https://catonmat.net/bash-one-liners-explained-part-three)