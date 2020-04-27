---
title: Linux 时间设置
date: 2017-12-02
modify_date: 2019-09-12
tags: Linux Date
key: 2017-12-02-Linux-Datetime
---

## 时区

[Time Zone Abbreviations – Worldwide List][1]

- **UTC (Coordinated Universal Time)**  
  协调世界时，是个标准时间。

- **GMT (Greenwich Mean Time)**  
  格林威治时间，是时区时间。

<!--more-->

Although GMT and UTC share the same current time in practice, there is a basic difference between the two:

- **GMT is a time zone** officially used in some European and African countries. The time can be displayed using both the 24-hour format (0 - 24) or the 12-hour format (1 - 12 am/pm).

- **UTC is not a time zone**, but a time standard that is the basis for civil time and time zones worldwide. This means that no country or territory officially uses UTC as a local time.

Time Zone Abbreviations

- **DST (Daylight Saving Time)**: 夏令时

- **CST (China Standard Time)**: 中国标准时间，UTC +8

- **CST (Central Standard Time)**: 美国中部时间，UTC -6  
  **CT (Central Time)**: UTC -6/-5

- **EDT (Eastern Daylight Time)**: 美国东部时间，UTC -4  
  **ET (Eastern Time)**: UTC -5/-4

- **PDT (Pacific Daylight Time)**: 太平洋时间，美国西部时间，UTC -7

- **CET (Central European Time)**: 欧洲中部时间，UTC +1  
  **ECT (European Central Time)**

- **CEST (Central European Summer Time)**: 欧洲中部夏季时间，UTC +2  
  **CEDT (Central European Daylight Time)**  
  **ECST (European Central Summer Time)**

- **EET (Eastern European Time)**: 欧洲东部时间，UTC +2

- **EEST (Eastern European Summer Time)**: 欧洲东部夏季时间，UTC +3  
  **EEDT (Eastern European Daylight Time)**

- **WET (Western European Time)**: 欧洲西部时间，UTC +0  
  **GMT (Greenwich Mean Time)**: 格林威治时间

- **WEST (Western European Summer Time)**: 欧洲西部夏季时间，UTC +1  
  **WEDT (Western European Daylight Time)**

## 系统时区文件

[What's the difference between **localtime** and **timezone** files?][2]

- **/etc/timezone**  
  是一个用户可读的纯文本文件，内容是你当前的时区。它可以写成 GMT/UTC 的偏移，但更多时候写成 `/usr/share/zoneinfo` 下的相对路径，指向合适的时区文件。例如，在中国，它可以写成 `Asia/Shanghai`。它的目的是增加时区的可读性，另外就是当 `/usr/share/zoneinfo` 目录下的时区文件更新时，确保 `/etc/localtime` 也能正确的更新。不过大部分系统通常将 `/etc/localtime` 直接设置成指向合适时区文件的软链接.

- **/etc/localtime**  
  是一个二进制文件，内容是计算系统时间的规则 (相对于 **Unix time**，**Unix time** 是内核的内部表示，测量从 1970-01-01 00:00:00 UTC 以来过去了多少秒).

### 显示当前时区

在支持 `systemd` Linux系统上，使用 `timedatectl` 命令

```zsh
$ timedatectl
      Local time: Mon 2019-08-05 10:02:54 CST
  Universal time: Mon 2019-08-05 02:02:54 UTC
        RTC time: Sun 2019-08-04 08:11:38
       Time zone: Asia/Shanghai (CST, +0800)
 Network time on: yes
NTP synchronized: no
 RTC in local TZ: no
```

使用 `tzselect` 命令查看可用的系统时区，该命令不会修改系统时区

```zsh
tzselect
```

使用 `readlink` 命令查看 `/etc/localtime` 指向的时区文件

```zsh
$ readlink /etc/localtime
/usr/share/zoneinfo/Asia/Shanghai
```

查看 `/etc/timezone` 文件

```zsh
$ cat /etc/timezone
Asia/Shanghai
```

### 修改系统时区

在支持 `systemd` Linux系统上，使用 `timedatectl set-timezone` 命令

```zsh
timedatectl set-timezone Asia/Shanghai
```

直接修改 `/etc/localtime` 指向的时区文件

```zsh
ln -s -f /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

在 RedHat Linux 和 CentOS 系统上使用 `timeconfig` 命令

```zsh
timeconfig
```

在 Debian/Ubuntu 等类似系统上使用 `dpkg-reconfigure` 工具

```zsh
dpkg-reconfigure tzdata
```

在脚本中指定时区，使用 `TZ` 环境变量

```zsh
TZ='Asia/Shanghai'
```

## 系统时间

### 查看系统时间

[man date](http://man7.org/linux/man-pages/man1/date.1.html)

```zsh
# output date and time in RFC 2822 format.
# Example: Mon, 14 Aug 2006 02:34:56 -0600
date -R

# -I[FMT], --iso-8601[=FMT]. output date/time in ISO 8601 format.
# FMT='date' for date only (the default), 'hours', 'minutes', 'seconds',
# or 'ns' for date and time to the indicated precision.
# Example: 2006-08-14T02:34:56-0600
date -Ins

# date +FORMAT
date +"%Y-%m-%d %H:%M:%S"

# Show the China time
TZ='Asia/Shanghai' date
```

### 设置系统时间

在支持 `systemd` Linux系统上，使用 `timedatectl set-time` 命令

```zsh
timedatectl set-time '2019/08/05 12:55:00'
```

使用 `date` 命令修改时间

```zsh
date -s '2019/08/05 13:52:00'

# 单独修改日期
date -s '2019/08/05'

# 单独修改时间
date -s '13:52:00'

# 将当前时间和日期写入BIOS，避免重启后失效
hwclock -w
```

[1]: https://www.timeanddate.com/time/zones/
[2]: https://unix.stackexchange.com/questions/384971/whats-the-difference-between-localtime-and-timezone-files