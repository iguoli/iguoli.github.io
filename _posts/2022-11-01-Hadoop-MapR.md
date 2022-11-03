---
title: Hadoop and MapR
date: 2022-11-01
modify_date: 2022-11-01
tags: BigData
key: Hadoop-MapR-2022-11-01
---

## hadoop fs and hadoop dfs command

[StackOverflow - Difference between "hadoop fs" and "hdfs dfs"](https://stackoverflow.com/questions/18142960/whats-the-difference-between-hadoop-fs-shell-commands-and-hdfs-dfs-shell-co)

```sh
hadoop fs <args>
```

`fs` 处理通用文件系统相关操作，它可以指向任何文件系统，因此，当处理不同的文件系统时，如 Local FS、HDFS、(S)FTP、S3 等，可以使用它。

- [`hadoop fs`](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/FileSystemShell.html) command guide
- [`hadoop`](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/CommandsManual.html) command guide

<!--more-->

```sh
hadoop dfs <args>
```

`dfs` 特定于 HDFS，适用于与 HDFS 相关的操作。该命令已被弃用，应该使用 `hdfs dfs` 代替。

```sh
hdfs dfs <args>
```

与 `hadoop dfs` 相同，即适用于与 HDFS 相关的所有操作，并且是推荐的命令。因此，即使使用 `hadoop dfs`，系统也会查找 hdfs 并将命令委托给 `hdfs dfs`。

- [`hdfs`](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html) command guide

## hadoop mfs command

```sh
hadoop mfs <args>
```

`hadoop mfs` 命令可以显示目录信息和内容，创建符号链接和硬链接，设置、获取和删除文件和目录的访问控制表达式 (**Access Control Expression, ACE**)，设置目录的压缩选项和块大小。

- [`hadoop mfs`](https://docs.datafabric.hpe.com/70/ReferenceGuide/hadoop-mfs.html) command guide

### hadoop mfs command example

```sh
hadoop mfs -ls <path>
```

列出由 `<path>` 指定的目录中的文件。 `hadoop mfs -ls` 命令对应于标准 `hadoop fs -ls` 命令，但提供了以下附加信息：

- Volume indicator `v` before POSIX mode bits
- 每个文件使用的块
- 每个块所在的服务器
- 是否为每个文件启用压缩
- 是否为每个文件启用加密
- 是否为每个文件启用 (A) 或禁用 (U) 审核

如果指定的路径是卷 (Volume)，则在 POSIX 模式位之前的给出的文件类型是 `v`。

如果给定文件或目录设置了 ACE 和 POSIX 模式位，则模式位最后会有一个加号 (`+`)，如果文件或目录上的 ACE 为空字符串，则没有加号。

```sh
hadoop mfs -getace [-R] <path>
```

返回给定文件或目录的访问权限，包括 POSIX 模式位和 ACE。当指定了 `-R` 时启用递归。
