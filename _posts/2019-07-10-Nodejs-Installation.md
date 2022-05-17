---
title: 设置 Nodejs 环境
date: 2019-07-10
modify_date: 2022-05-12
tags: Javascript
key: Nodejs-Installation-2019-07-10
---

## Linux 系统下手工安装 [Nodejs](https://github.com/nodejs/help/wiki/Installation)

### 从官方下载 [Nodejs](https://nodejs.org/zh-cn/download/) 安装包

### 解压安装包到 `/usr/local/lib/nodejs`

```bash
VERSION=v10.16.0
DISTRO=linux-x64
sudo mkdir -p /usr/local/lib/nodejs
sudo tar -xvJf node-$VERSION-$DISTRO.tar.xz -C /usr/local/lib/nodejs
```

<!--more-->

### 在 `/usr/bin` 目录下创建软链接

```bash
sudo ln -s /usr/local/lib/nodejs/node-$VERSION-$DISTRO/bin/node /usr/bin/node

sudo ln -s /usr/local/lib/nodejs/node-$VERSION-$DISTRO/bin/npm /usr/bin/npm

sudo ln -s /usr/local/lib/nodejs/node-$VERSION-$DISTRO/bin/npx /usr/bin/npx
```

### 检查安装是否成功

- `$ node -v`

- `$ npm version`
  
- `$ npx -v`

得到类似于下面的输出

```bash
$ node -v
v10.16.0

$ npm version
{ npm: '6.9.0',
  ares: '1.15.0',
  brotli: '1.0.7',
  cldr: '35.1',
  http_parser: '2.8.0',
  icu: '64.2',
  modules: '64',
  napi: '4',
  nghttp2: '1.34.0',
  node: '10.16.0',
  openssl: '1.1.1b',
  tz: '2019a',
  unicode: '12.1',
  uv: '1.28.0',
  v8: '6.8.275.32-node.52',
  zlib: '1.2.11' }
```

### 在启动文件 `~/.profile (bash)` 或 `~/.zshenv (zsh)` 中设置环境变量

```bash
# Nodejs
VERSION=v10.16.0
DISTRO=linux-x64
export PATH=/usr/local/lib/nodejs/node-$VERSION-$DISTRO/bin:$PATH
```

### 重新应用启动文件

```bash
# bash
source ~/.profile

# zsh
source ~/.zshenv
```

## 配置 npm 国内镜像源

```bash
# get current registry
npm config get registry

# use taobao mirror
npm config set registry https://registry.npm.taobao.org

# check the config in the .npmrc file
cat ~/.npmrc
```

```text
registry=https://registry.npm.taobao.org
```

## macOS 系统安装 Nodejs

使用 Homebrew 安装

```bash
brew install nodejs
```

## Windows 系统安装 Nodejs

使用 scoop 安装

```bash
scoop install nodejs
```

安装完成后退出当前shell并重新打开

## Nodejs 版本管理工具 [nvm](https://github.com/nvm-sh/nvm)

### Windows 安装与用法

```bash
scoop install nvm
```

列出可安装的版本

```bash
nvm list available

# "ls" is an alias for "list"
nvm ls available
```

安装最新版本

```bash
nvm install latest

# "node" is an alias for the latest version
nvm install node
```

安装最新长期支持 (LTS) 版本

```bash
nvm install lts
```

安装指定版本

```bash
nvm install 18.1.0
```

### macOS 安装与用法

```bash
brew install nvm
```

依据 brew 安装后的提示，设置环境变量和自动补全

```bash
$ vim ~/.zshrc

# nvm
export NVM_DIR="$HOME/.nvm"
[ -s "/opt/homebrew/opt/nvm/nvm.sh" ] && \. "/opt/homebrew/opt/nvm/nvm.sh"  # This loads nvm
[ -s "/opt/homebrew/opt/nvm/etc/bash_completion.d/nvm" ] && \. "/opt/homebrew/opt/nvm/etc/bash_completion.d/nvm"  # This loads nvm bash_completion
```

列出所有可安装版本

```bash
nvm ls-remote

nvm ls-remote --lts
```

列出已安装版本

```bash
nvm ls
```

安装最新版本

```bash
nvm install node
```

安装最新长期支持版本

```bash
brew install --lts
```

设置默认使用版本

```bash
nvm alias default node

nvm alias default 'lts/*'
```

查看当前使用版本

```bash
nvm current
```

在当前 *shell* 使用指定版本

```bash
nvm use node

nvm use --lts
```

使用系统自带版本或查看系统安装版本

```bash
nvm use system

nvm run system --version
```
