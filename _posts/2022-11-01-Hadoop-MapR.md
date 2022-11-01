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

`fs` relates to a generic file system which can point to any file systems like local, HDFS etc. So this can be used when you are dealing with different file systems such as Local FS, (S)FTP, S3, and others.

- [`hadoop fs`](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/FileSystemShell.html) command guide
- [`hadoop`](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/CommandsManual.html) command guide

```sh
hadoop dfs <args>
```

`dfs` is very specific to HDFS. would work for operation relates to HDFS. **This has been deprecated and we should use `hdfs dfs` instead**.

```sh
hdfs dfs <args>
```

same as 2nd i.e would work for all the operations related to HDFS and is the recommended command instead of `hadoop dfs`.

So even if you use `hadoop dfs` , it will look locate hdfs and delegate that command to `hdfs dfs`.

- [`hdfs`](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html) command guide

## hadoop mfs command

```sh
hadoop mfs <args>
```

The `hadoop mfs` command displays directory information and contents, creates symbolic links and hard links, sets, gets, and removes Access Control Expressions (ACE) on files and directories, and sets compression and chunk size on a directory.

- [`hadoop mfs`](https://docs.datafabric.hpe.com/70/ReferenceGuide/hadoop-mfs.html) command guide

### hadoop mfs command example

```sh
hadoop mfs -ls <path>
```

Lists files in the directory specified by path. The `hadoop mfs -ls` command corresponds to the standard `hadoop fs -ls` command, but provides the following additional information:

- Volume indicator `v` before POSIX mode bits
- Chunks used for each file
- Server where each chunk resides
- Whether compression is enabled for each file
- Whether encryption is enabled for each file
- Whether audit is enabled (A) or disabled (U) for each file

```sh
hadoop mfs -getace [-R] <path>
```

Returns the permissions -- POSIX mode bits and ACEs -- for the given file or (recursively) for the directory. Recursion is enabled only if `-R` is specified; if `-R` is not specified, this parameter returns the permissions only for the given directory. Here:

- `[-R]` — (Optional) Enables recursion allowing you to perform the operation in subdirectories as well.
- path — (Required) Specifies the path to the file or directory.

If one or more ACEs are available for the file or directory, a plus sign (+), which indicates that both ACEs and POSIX mode bits are set for the given file or directory, is returned. If the ACE on the file or directory is an empty string, the plus sign is not returned.