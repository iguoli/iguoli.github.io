---
title: XDG Base Directory Specification
date: 2020-06-06
modify_date: 2020-06-06
tags: Linux
key: XDG-Base-Directory-Specification-2020-06-06
---

[XDG Base Directory Specification]（XDG 基本目录规范）是由 [Freedesktop.org] 制定，旨在规范应用程序文件的存储路径，解决用户主目录下各种点文件的无序状态。

<!--more-->

## 环境变量

| 变量名 | 用途 | 默认值 |
| -- | -- | -- |
|`$XDG_DATA_HOME`| 存放用户数据文件的基本目录 | `$HOME/.local/share` |
|`$XDG_CONFIG_HOME`| 存放用户配置文件的基本目录 | `$HOME/.config` |
|`$XDG_DATA_DIRS`| 用户数据文件基本目录的搜索路径，多个路径以冒号（`:`）分隔 | `/usr/local/share/:/usr/share/` |
|`$XDG_CONFIG_DIRS`| 用户配置文件基本目录的搜索路径，多个路径以冒号（`:`）分隔 | `/etc/xdg` |
|`$XDG_CACHE_HOME`| 存放用户缓存文件的基本目录 | `$HOME/.cache` |
|`$XDG_RUNTIME_DIR`| 存放用户运行时文件（如 sockets, named pipes）的基本目录，该目录的权限必须是 `0700` | |

[Freedesktop.org]: https://www.freedesktop.org/
[XDG Base Directory Specification]: https://specifications.freedesktop.org/basedir-spec/basedir-spec-latest.html
