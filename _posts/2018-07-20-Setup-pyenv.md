---
title:  pyenv - Python 版本管理工具
date: 2018-07-20
modify_date: 2021-12-14
tags: Python
key: Setup-pyenv-2018-07-20
---

## 安装 [pyenv](https://github.com/pyenv/pyenv) 和 [pyenv-virtualenv](https://github.com/pyenv/pyenv-virtualenv) 插件

- [使用 Homebrew 安装](https://github.com/pyenv/pyenv#homebrew-in-macos)

```bash
brew update

brew install pyenv

brew install pyenv-virtualenv
```

<!--more-->

- [使用 Git 安装](https://github.com/pyenv/pyenv#basic-github-checkout)

```bash
git clone https://github.com/pyenv/pyenv.git ~/.pyenv

git clone https://github.com/pyenv/pyenv-virtualenv.git $(pyenv root)/plugins/pyenv-virtualenv
```

### 配置 pyenv 和 pyenv-virtualenv

对于 `Zsh`

- 如果是 Homebrew 安装

  - 在  `~/.zprofile` 添加

    ```bash
    if type pyenv >/dev/null 2>&1; then;
        eval "$(pyenv init --path)"
    fi
    ```

  - 在 `~/.zshrc` 添加

    ```bash
    if type pyenv >/dev/null 2>&1; then;
        eval "$(pyenv init -)"
    fi

    if type pyenv-virtualenv >/dev/null 2>&1; then;
        eval "$(pyenv virtualenv-init -)"
    fi
    ```

- 如果是 Git 安装

  - 在  `~/.zprofile` 添加

    ```bash
    export PYENV_ROOT="$HOME/.pyenv"
    export PATH="$PYENV_ROOT/bin:$PATH"
    if type pyenv >/dev/null 2>&1; then;
        eval "$(pyenv init --path)"
    fi
    ```

  - 在 `~/.zshrc` 添加

    ```bash
    if type pyenv >/dev/null 2>&1; then;
        eval "$(pyenv init -)"
    fi

    if type pyenv-virtualenv >/dev/null 2>&1; then;
        eval "$(pyenv virtualenv-init -)"
    fi
    ```

## 卸载 pyenv

1. 删除在 `~/.zprofile` 和 `~/.zshrc` 中的配置
2. 删除 pyenv 的根目录，这将删除所有安装的 Python 版本

   ```bash
   rm -rf $(pyenv root)
   ```

   如果是 Homebrew 安装，还要执行

   ```bash
   brew uninstall pyenv
   ```

## 安装 python

- 可以通过设置 `http_proxy` 和 `https_proxy` 代理来加速下载过程。

- 或者，将提前下载好的 `Python-$version.tar.xz` 文件放到 `$(pyenv root)/cache/` 目录下，`pyenv` 会跳过下载直接安装。

使用 `pyenv` 安装 python 时遇到编译问题，请先参考

- [Troubleshooting / FAQ](https://github.com/pyenv/pyenv/wiki)

- [Common build problems](https://github.com/pyenv/pyenv/wiki/Common-build-problems)

从 3.9.1 版本开始，Python 已原生支持 M1 Chip (Apple Silicon)，在 M1 的 macOS 系统安装 python，需要注意如果使用 Homebrew 安装了 GNU coreutils，不能在 `PATH` 中添加 coreutils 的 bin 目录。
{:.info}

### pyenv 命令

```bash
# 查看可安装的Python版本
pyenv install -l

# 安装某个版本的Python
pyenv install 3.10.0

# 卸载某个版本的Python
pyenv uninstall 3.10.0

# 查看已安装的Python
pyenv versions

# 查看当前的Python版本
pyenv version

# 查看可用的pyenv命令
pyenv commands
```

### pyenv virtualenv 命令

```bash
# 使用指定Python版本创建virtualenv环境
pyenv virtualenv 3.10.0 jupyter-lab

# 使用当前Python版本创建virtualenv环境
pyenv virtualenv jupyter-lab

# 激活virtualenv环境
pyenv activate jupyter-lab

# 退出virtualenv环境
pyenv deactivate

# 自动激活和退出vritualenv环境
pyenv local jupyter-lab
```
