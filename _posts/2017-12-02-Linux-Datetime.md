---
title: Linux datetime
date: 2017-12-02
modify_date: 2022-01-11
tags: Linux Date
key: Linux-Datetime-2017-12-02
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

- **EST (Eastern Standard Time)**: 美国东部时间，UTC -5  
  **EDT (Eastern Daylight Time)**: 美国东部时间，UTC -4  
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
  是一个用户可读的纯文本文件，内容是你当前的时区。它可以写成 GMT/UTC 的偏移，但更多时候写成 `/usr/share/zoneinfo` 下的相对路径，指向合适的时区文件。例如，在中国，它可以写成 `Asia/Shanghai`。它的目的是增加时区的可读性，另外就是当 `/usr/share/zoneinfo` 目录下的时区文件更新时，确保 `/etc/localtime` 也能正确的更新。**不过大部分系统通常将 `/etc/localtime` 直接设置成指向合适时区文件的软链接**。

- **/etc/localtime**  
  是一个二进制文件，内容是计算系统时间的规则 (相对于 **Unix time**，**Unix time** 是内核的内部表示，测量从 1970-01-01 00:00:00 UTC 以来过去了多少秒)。

### 显示当前时区

在支持 `systemd` Linux系统上，使用 `timedatectl` 命令

```sh
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

```sh
tzselect
```

使用 `readlink` 命令查看 `/etc/localtime` 指向的时区文件

```sh
$ readlink /etc/localtime
/usr/share/zoneinfo/Asia/Shanghai
```

查看 `/etc/timezone` 文件

```sh
$ cat /etc/timezone
Asia/Shanghai
```

### 修改系统时区

在支持 `systemd` Linux系统上，使用 `timedatectl set-timezone` 命令

```sh
timedatectl set-timezone Asia/Shanghai
```

直接修改 `/etc/localtime` 指向的时区文件

```sh
ln -s -f /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

在 RedHat Linux 和 CentOS 系统上使用 `timeconfig` 命令

```sh
timeconfig
```

在 Debian/Ubuntu 等类似系统上使用 `dpkg-reconfigure` 工具

```sh
dpkg-reconfigure tzdata
```

在脚本中指定时区，使用 `TZ` 环境变量

```sh
TZ='Asia/Shanghai'
```

## 系统时间

### 查看时间

[man date](http://man7.org/linux/man-pages/man1/date.1.html)

给 `date` 命令设置 `TZ` 环境变量来显示指定时区的时间

```sh
TZ=Asia/Shanghai date
```

显示 RFC 5322 格式时间，例如 `Tue, 11 Jan 2022 12:30:20 +0800`

```sh
date -R

date --rfc-email
```

显示 RFC 3339 格式时间，例如 `2022-01-11 12:30:20+08:00`

> --rfc-3339=FMT 有效的时间参数为
>
> - `date`
> - `seconds`
> - `ns`
>

```sh
date --rfc-3339=s
```

显示 ISO 8601 格式时间，例如 `2022-01-11T12:30:20+08:00`
> `-I[FMT], --iso-8601[=FMT]`. `-I` 默认只显示到日期，相当于 `-Id` 或 `-Idate`，
> 该参数的有效时间参数为
>
> - `date`
> - `hours`
> - `minutes`
> - `seconds`
> - `ns`
>

```sh
# 只显示日期
date -I

# 显示秒级 iso-8601 时间
date -Is

# 显示纳秒级 iso-8601 时间
date -Ins

# 显示中国时区 iso-8601 时间
TZ='Asia/Shanghai' date -Is
```

显示自定义格式时间

```sh
date +"%Y-%m-%d %H:%M:%S"
```

显示 UTC 时间，例如 `2022-01-11 04:30:20`

```sh
# long term
date --utc

# short term
date -u

date -u -R

date -u +"%Y-%m-%d %H:%M:%S"
```

打印 timestamp

```sh
date +%s
```

### 时间转换

显示由 `--date=STRING` 描述的时间

> The --date=STRING is a mostly free format human readable date string such as
> "Sun, 29 Feb 2004 16:21:42 -0800" or "2004-02-29 16:21:42" or even "next
> Thursday".  A date string may contain items indicating calendar date, time
> of day, time zone, day of week, relative time, relative date, and numbers.
> An empty string indicates the beginning of the day.  The date string format
> is more complex than is easily documented here but is fully described in the
> info documentation.

将 timestamp 转换成指定格式的时间

```sh
# 转换为系统当前格式时间
date -d '@1664174673'

date -d '@1664174673' -R

date -d '@1664174673' -R -u
```

将美国芝加哥时间 (UTC -5) 早上4点转换为当前系统时间

```sh
date -d 'TZ="America/Chicago" 4am'
```

将美国东部时间转换为当前系统时间

```sh
date -d '2022-01-10 20:29:03.947 EST'
```

将美国东部时间转换为中国时间

```sh
TZ='Asia/Shanghai' date -d '2022-01-10 20:29:03.947 EST'
```

将美国东部时间转换为中国时间并以 ISO 8601 格式显示到秒

```sh
TZ='Asia/Shanghai' date -d '2022-01-10 20:29:03.947 EST' -Is
```

将 UTC 时间转换为中国时间并以 RFC 3339 格式显示到秒

```sh
TZ='Asia/Shanghai' date -d '2022-01-10T20:29:03.947 UTC' --rfc-3339=s
```

将 RFC 3339 格式 UTC -5 时区时间转换为中国时间并以 ISO 8601 格式显示到秒

```sh
TZ='Asia/Shanghai' date -d '2022-01-10 22:38:02.162-05' -Is
```

### 设置系统时间

在支持 `systemd` Linux系统上，使用 `timedatectl set-time` 命令

```sh
timedatectl set-time '2019/08/05 12:55:00'
```

使用 `date` 命令修改时间

```sh
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