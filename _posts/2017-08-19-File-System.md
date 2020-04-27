---
title: Linux 文件系统
date: 2017-08-19
modify_date: 2019-08-15
tags: Linux Filesystem
key: 2017-08-19-File-System
---

## EXT4

Ext4是第四代扩展文件系统的缩写，它是2008年推出的。它是一个真正可靠的文件系统，它几乎在过去几年的大部分发行版中一直是默认选项，它是由比较老的代码生成的。它是一个日志文件系统，意味着它会对文件在磁盘中的位置以及任何其它对磁盘的更改做记录。如果系统崩溃，得益于journal技术，文件系统很少会损坏。

最大单个文件大小可以从16 GB到16 TB
最大文件系统大小为1EB（exabyte）
最大值包含64,000个子目录（ext3中的32,000个）

## XFS

XFS 是由 SGI 为其 IRIX 平台设计的高性能64位日志文件系统。 XFS 具有各种改进，使其能够在文件系统群体列表中脱颖而出，例如用于元数据操作的日志记录，可扩展/并行I/O，挂起/恢复I/O，在线碎片整理，延迟性能分配，等等

大概在2002年，XFS 被合入 Linux 内核，2009年 RHEL Linux 版本 5.4 使用了 XFS 文件系统。 由于其高性能，架构可扩展性和鲁棒性，XFS 一直是很多企业系统的首选，特别是拥有大量数据的企业系统。 现在，RHEL/CentOS 7 和 Oracle Linux 使用 XFS 作为其默认文件系统。

最大单个文件大小可以是16 TB到16 Exabytes
最大文件系统大小为8EB（exabyte）
缺点：XFS文件系统不能缩小，当删除大量文件时会性能下降。

## 常用命令

| **Task**                       | **ext3/4**             | **XFS**                        |
| ------------------------------ | ---------------------- | ------------------------------ |
| Create a file system           | mkfs.ext4 or mkfs.ext3 | mkfs.xfs                       |
| File system check              | e2fsck                 | xfs_repair                     |
| Resizing a file system         | resize2fs              | xfs_growfs                     |
| Save an image of a file system | e2image                | xfs_metadump and xfs_mdrestore |
| Label or tune a file system    | tune2fs                | xfs_admin                      |
| Backup a file system           | dump and restore       | xfsdump and xfsrestore         |

## 参考链接

[XFS vs EXT4](http://xiaqunfeng.cc/2017/07/06/XFS-vs-EXT4/)
