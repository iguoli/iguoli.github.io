---
title: macOS Tips
date: 2021-10-28
modify_date: 2021-10-28
tags: macOS
key: macOS-Tips-2021-10-28
---

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