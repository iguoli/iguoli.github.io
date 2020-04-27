---
title: MacOS 常用配置
date: 2017-05-20
modify_date: 2019-10-03
tags: MacOS GNU Utils
key: MacOS-Utils-2017-05-20
---

## 在 MacOS/OS X 上安装和使用 GNU 命令行工具

MacOS 系统默认使用 BSD 版本的命令行工具，这与 Linux 系统的命令行工具不同，但这两个版本都符合 POSIX 标准。我们可以使用 Homebrew 安装 GNU 命令行工具。

```zsh
brew install coreutils
```

<!--more-->

GNU coreutils 包含许多基本的 Unix 命令，例如 `ls`，`cat` 等。还可以选择安装其它 GNU 工具

```zsh
brew install binutils
brew install diffutils
brew install ed --default-names
brew install findutils --with-default-names
brew install gawk
brew install gnu-indent --with-default-names
brew install gnu-sed --with-default-names
brew install gnu-tar --with-default-names
brew install gnu-which --with-default-names
brew install gnutls
brew install grep --with-default-names
brew install gzip
brew install screen
brew install watch
brew install wdiff --with-gettext
brew install wget
```

另外，MacOS/OS X 自带的一些 GNU 命令行工具可能比较老了，你可以更新到较新的版本

```zsh
brew install bash
brew install emacs
brew install gdb  # gdb requires further actions to make it work. See `brew info gdb`.
brew install gpatch
brew install m4
brew install make
brew install nano
```

将命令路径设置在 PATH 最前面.

```zsh
export PATH="/usr/local/opt/coreutils/libexec/gnubin:$PATH"
export MANPATH="/usr/local/opt/coreutils/libexec/gnuman:$MANPATH"
```

`dircolors` 命令可以使 `ls` 命令彩色化输出. 可以参考 `oh-my-zsh` 中 `~/.oh-my-zsh/lib/theme-and-appearance.zsh` 的定义.

```zsh
dircolors > ~/.dircolors
```

## 使用 `grc` 命令让其它命令彩色化输出

``````zsh
brew install grc

grc diff -u file1 file2
```
