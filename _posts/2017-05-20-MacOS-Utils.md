---
title: macOS 常用命令行工具
date: 2017-05-20
modify_date: 2020-05-18
tags: macOS GNU Utils
key: macOS-Utils-2017-05-20
---

## 在 macOS/OS X 上安装和使用 GNU 命令行工具

macOS 系统默认使用 BSD 版本的命令行工具，这与 Linux 系统的命令行工具不同，但这两个版本都符合 POSIX 标准。我们可以使用 Homebrew 安装 GNU 命令行工具。

```sh
brew install coreutils
```

<!--more-->

GNU coreutils 包含许多基本的 Unix 命令，例如 `ls`，`cat` 等。具体有哪些命令可到 `/usr/local/opt/coreutils/libexec/gnubin` 下查看。

此外，还可以选择安装其它 GNU 工具

```sh
brew install binutils
brew install diffutils
brew install ed
brew install findutils
brew install gawk
brew install gnu-indent
brew install gnu-sed
brew install gnu-tar
brew install gnu-which
brew install gnutls
brew install grep
brew install gzip
brew install screen
brew install watch
brew install wdiff
brew install wget
```

另外，macOS/OS X 自带了一些 GNU 命令行工具，你可以更新到较新的版本

```sh
brew install bash
brew install less
brew install emacs
brew install gdb  # gdb requires further actions to make it work. See `brew info gdb`.
brew install gpatch
brew install m4
brew install make
brew install nano
```

还有一些命令不是来自 GNU，但是可以替换系统自带的比较旧的命令

```sh
brew install file-formula # 替换系统自带的 file 命令
brew install git
brew install openssh
brew install perl
brew install python
brew install rsync
brew install svn
brew install unzip
brew install vim
brew install macvim
brew install zsh
```

将命令路径设置在 PATH 最前面.

```sh
export PATH="/usr/local/opt/coreutils/libexec/gnubin:$PATH"
export PATH="/usr/local/opt/file-formula/bin:$PATH"
export MANPATH="/usr/local/opt/coreutils/libexec/gnuman:$MANPATH"
export MANPATH="/usr/local/opt/file-formula/share/man/man1:$MANPATH"
```

`dircolors` 命令可以使 `ls` 命令彩色化输出. 可以参考 `oh-my-zsh` 中 `~/.oh-my-zsh/lib/theme-and-appearance.zsh` 的定义.

```sh
dircolors > ~/.dircolors
```

## 使用 grc 命令让其它命令彩色化输出

```sh
brew install grc

grc diff -u file1 file2
```

## 参考文档

[Install and Use GNU Command Line Tools on macOS/OS X](https://www.topbug.net/blog/2013/04/14/install-and-use-gnu-command-line-tools-in-mac-os-x/)