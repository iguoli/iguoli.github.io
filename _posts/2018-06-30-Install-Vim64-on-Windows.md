---
title: 在 Windows 系统上安装64位 Vim 
date: 2018-06-30
tags: Vim Windows
key: Install-Vim64-on-Windows-2018-06-30
---

1. 下载 vim x64 的 zip 包

- <https://github.com/vim/vim-win32-installer/releases>
- <https://tuxproject.de/projects/vim/>

2. 创建文件夹`C:\Program Files\Vim\vimxx`(xx 为 vim 版本，如 8.1 版本就是 vim81)，将解压的文件复制到该文件夹

<!--more-->

3. 使用管理员运行 cmd 命令，进入该目录，执行 install.exe 命令。

4. 如果鼠标右键没有"Edit with Vim"菜单，检查`C:\Program Files\Vim\vimxx`目录下是否有`GvimExt`文件夹，将其重命名为`GvimExt64`，然后重新执行第 3 步操作。

5. 安装 git for windows，

6. 在 Git-Bash 下安装 Vundle

```bash
git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim
```

7. 进入 Gvim，安装插件

```vim
:PluginInstall
```

**安装 YouCompleteme 插件的注意事项**

原文参考https://github.com/Valloric/YouCompleteMe#windows

- 安装的 Python 和 Vim 应该同为 32 位或同为 64 位
- 系统安装的 Python 版本应该与 Vim 编译时使用的 python2 或 python3 版本一致，可以在 Vim 中使用`:version`命令，查看`-DDYNAMIC_PYTHON_DLL=\"python27.dll\" and -DDYNAMIC_PYTHON3_DLL=\"python36.dll\"`，该命令指出 Vim 编译时是使用的 Python2.7 和 Python3.6 版本。