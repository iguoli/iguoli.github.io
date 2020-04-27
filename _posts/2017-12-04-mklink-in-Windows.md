---
title: Windows 下创建目录链接
author: Guo Li
date: 2017-12-04
tags: Windows
key: mklink-in-Windows-2017-12-04
---

在 Windows 下如果通过`右键菜单->创建快捷方式`生成的文件或文件夹，其实是生成了一个后辍为.lnk 的文件，在其它程序里面是不会引用到原文件或文件夹的，如果通过`mklink`命令来创建快捷方式（符号链接），则其它程序会认为这就是一个真正的文件或文件夹。

<!--more-->

比如使用 Dropbox 同步文件夹，如果不想把要同步的文件夹复制到 Dropbox 下，可以通过`mklink`命令来创建一个该文件夹的快捷方式到 Dropbox 下，就可以实现文件的同步

```cmd
> mklink
创建符号链接。

MKLINK [[/D] | [/H] | [/J]] Link Target

        /D      创建目录符号链接。默认为文件
                符号链接。
        /H      创建硬链接而非符号链接。
        /J      创建目录联接。
        Link    指定新的符号链接名称。
        Target  指定新链接引用的路径
                (相对或绝对)。
```

例如在 C:\Dropbox 里创建一个 D:\books 目录的快捷方式

```cmd
> mklink /j C:\Dropbox\books D:\books
为 c:\Dropbox\books <<===>> d:\books 创建的联接
```

可以使用`rmdir`命令删除该链接

```cmd
> rmdir C:\Dropbox\books
```