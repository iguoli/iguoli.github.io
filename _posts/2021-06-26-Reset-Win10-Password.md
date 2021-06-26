---
title: 重置 Win10 密码
date: 2021-06-26
modify_date: 2021-06-26
tags: Windows
key: Reset-Win10-Password-2021-06-25
---

1. 使用优盘进入 WinPE 系统 (推荐优启通)

2. 打开命令行程序

    ```bat
    C:
    cd C:\Windows\System32
    move Utilman.exe Utilman.exe.bak
    copy cmd.exe Utilman.exe
    ```

3. 拔掉优盘重启系统

4. 在登录界面点击屏幕右下角的 **轻松使用** 图标打开命令行工具

    ```bat
    cd C:\Windows\System32
    control userpasswords2
    ```

5. 在弹出的对话框中可以重置用户密码。
