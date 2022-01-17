---
title: macOS Tips
date: 2021-10-28
modify_date: 2021-10-28
tags: macOS
key: macOS-Tips-2021-10-28
---

## 启动台无法切换页面

经常在拖动图标到不同页面的时候发现启动台切换页面失效，会自动切回到第一页，在终端使用下面的命令可以重启控制台

```bash
killall Dock
```

## 查看系统信息

所有系统信息

```bash
osascript -e "system info"
```

IP 地址

```bash
osascript -e "IPv4 address of (system info)"
```

## 通过命令行唤醒和睡眠系统

在充电状态下，macbook 睡眠后可以仍可以保持 ssh 链接，可以通过以下命令对 macbook 进行远程操作

<!--more-->

- 唤醒屏幕

```bash
caffeinate -u -t2
```

选项 `-t 2` 告诉 `caffeinate` 2 秒后退出，否则需要 `ctrl+c` 才能退出命令。

- 关闭屏幕

```bash
pmset displaysleepnow
```

- 系统睡眠

```bash
osascript -e 'tell application "System Events" to sleep'
```
