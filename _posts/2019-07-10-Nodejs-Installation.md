---
title: Linux 上安装 Nodejs
date: 2019-07-10
modify_date: 2020-05-13
tags: Javascript
key: Nodejs-Installation-2019-07-10
---

本文引用地址 [Nodejs help wiki](https://github.com/nodejs/help/wiki/Installation)

## 从官方下载 Nodejs 安装包

[官方下载地址](https://nodejs.org/zh-cn/download/)

<!--more-->

### 解压安装包到 `/usr/local/lib/nodejs`

```zsh
VERSION=v10.16.0
DISTRO=linux-x64
sudo mkdir -p /usr/local/lib/nodejs
sudo tar -xvJf node-$VERSION-$DISTRO.tar.xz -C /usr/local/lib/nodejs
```

### 在 `/usr/bin` 目录下创建软链接

```zsh
sudo ln -s /usr/local/lib/nodejs/node-$VERSION-$DISTRO/bin/node /usr/bin/node

sudo ln -s /usr/local/lib/nodejs/node-$VERSION-$DISTRO/bin/npm /usr/bin/npm

sudo ln -s /usr/local/lib/nodejs/node-$VERSION-$DISTRO/bin/npx /usr/bin/npx
```

### 检查安装是否成功

- `$ node -v`

- `$ npm version`
  
- `$ npx -v`

得到类似于下面的输出

```zsh
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

```zsh
# Nodejs
VERSION=v10.16.0
DISTRO=linux-x64
export PATH=/usr/local/lib/nodejs/node-$VERSION-$DISTRO/bin:$PATH
```

### 重新应用启动文件

```zsh
# bash
source ~/.profile

# zsh
source ~/.zshenv
```

## 使用 nodenv 管理 nodejs

### 安装 [nodenv](https://github.com/nodenv/nodenv)

在 MacOS 上使用 Homebrew 安装

```zsh
brew install nodenv

echo 'eval "$(nodenv init -)"' >> ~/.zshrc

source ~/.zshrc
```

克隆 git 仓库安装

```zsh
git clone https://github.com/nodenv/nodenv.git ~/.nodenv

# Optionally, try to compile dynamic bash extension to speed up nodenv.
# Don't worry if it fails, nodenv will still work normally.
cd ~/.nodenv && src/configure && make -C src

echo 'export PATH="$HOME/.nodenv/bin:$PATH"' >> ~/.zshrc

~/.nodenv/bin/nodenv init
```

### 安装插件

[node-build](https://github.com/nodenv/node-build) 插件
```zsh
mkdir -p "$(nodenv root)"/plugins
git clone https://github.com/nodenv/node-build.git "$(nodenv root)"/plugins/node-build
```

[node-build-update-defs](https://github.com/nodenv/node-build-update-defs) 插件

```zsh
git clone https://github.com/nodenv/node-build-update-defs.git "$(nodenv root)"/plugins/node-build-update-defs
```

获取最新的 nodejs 版本列表

```zsh
nodenv update-version-defs
```

### 使用 nodenv 安装 nodejs

```zsh
# list all available versions
nodenv install -l

# install a nodejs version
nodenv install 12.16.2
```

### 卸载 nodenv

```zsh
# for homebrew
brew uninstall nodenv

# for git clone
rm -rf `nodenv root`
```

## 配置 npm 国内镜像源

```zsh
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
