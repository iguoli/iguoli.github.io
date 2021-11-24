---
title:  macOS/Linux 下配置 Python 环境
date: 2018-07-20
tags: Python
key: Setup-Python-on-Linux-2018-07-20
---

使用 pyenv 在多个版本的 Python 之间切换。

<!--more-->

## 安装 [pyenv](https://github.com/pyenv/pyenv)

- 使用 Homebrew 安装

```bash
$ brew install pyenv

$ vim ~/.zshrc
eval "$(pyenv init -)"
```

- 从 Github 直接下载

```bash
$ git clone https://github.com/pyenv/pyenv.git ~/.pyenv

$ vim ~/.zshenv
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
if command -v pyenv 1>/dev/null 2>&1; then;
    eval "$(pyenv init -)"
fi

# 重新进入shell
$ exec "$SHELL"
```

## 为 pyenv 指定下载 python 文件的国内镜像

参考 [Can I change the source of downloading python?](https://github.com/pyenv/pyenv/issues/18)

`python-build` 是 `pyenv` 的一个插件，提供 `pyenv install` 命令来使用 `pyenv` 可以下载编译不同的 python 版本。该插件接受一个 `PYTHON_BUILD_MIRROR_URL` 环境变量来指定下载 python 的镜像地址。

```bash
export PYTHON_BUILD_MIRROR_URL="https://npm.taobao.org/mirrors/python/"
```

或者，将提前下载好的 `Python-$version.tar.xz` 文件放到 `$(pyenv root)/cache/` 目录下，pyenv 会跳过下载直接安装。

## 使用 pyenv 安装 python 时遇到编译问题，请先参考[Common build problems](https://github.com/pyenv/pyenv/wiki/Common-build-problems)

## 卸载 pyenv

1. 从~/.zshenv 文件删除环境变量及`pyenv init`
2. 删除 pyenv 的根目录

```bash
rm -rf $(pyenv root)
```

## pyenv 常用命令

```bash
# 查看可安装的Python版本
pyenv install -l

# 安装某个版本的Python
pyenv install 3.6.6

# 卸载某个版本的Python
pyenv uninstall 3.6.6

# 查看已安装的Python
pyenv versions

# 查看当前的Python版本
pyenv version

# 查看可用的pyenv命令
pyenv commands
```

## 安装 pyenv-virtualenv

```bash
git clone https://github.com/pyenv/pyenv-virtualenv.git $(pyenv root)/plugins/pyenv-virtualenv
```

## 设置 pyenv virtualenv 环境变量

```bash
$ vim ~/.zshenv
eval "$(pyenv virtualenv-init -)"

# 重新进入shell
exec "$SHELL"
```

## 使用 pyenv virtualenv

```bash
# 使用指定Python版本创建virtualenv环境
pyenv virtualenv 3.6.6 venv-name36

# 使用当前Python版本创建virtualenv环境
pyenv virtualenv venv-name36

# 激活virtualenv环境
pyenv activate venv-name36

# 退出virtualenv环境
pyenv deactivate

# 自动激活和退出vritualenv环境
pyenv local venv-name36
```