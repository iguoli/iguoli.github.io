---
title: Linux 上安装 Nodejs
date: 2019-07-10
tags: Javascript
key: 2019-07-10-Nodejs-Installation
---

Nodejs 安装步骤。

<!--more-->

本文引用地址 [Nodejs help wiki](https://github.com/nodejs/help/wiki/Installation)

## 下载 Nodejs 安装包

[官方下载地址](https://nodejs.org/zh-cn/download/)

## 解压安装包到 `/usr/local/lib/nodejs`

```zsh
VERSION=v10.16.0
DISTRO=linux-x64
sudo mkdir -p /usr/local/lib/nodejs
sudo tar -xvJf node-$VERSION-$DISTRO.tar.xz -C /usr/local/lib/nodejs
```

## 在 `/usr/bin` 目录下创建软链接

```zsh
sudo ln -s /usr/local/lib/nodejs/node-$VERSION-$DISTRO/bin/node /usr/bin/node

sudo ln -s /usr/local/lib/nodejs/node-$VERSION-$DISTRO/bin/npm /usr/bin/npm

sudo ln -s /usr/local/lib/nodejs/node-$VERSION-$DISTRO/bin/npx /usr/bin/npx
```

## 检查安装是否成功

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

## 在启动文件 `~/.profile (bash)` 或 `~/.zshenv (zsh)` 中设置环境变量

```zsh
# Nodejs
VERSION=v10.16.0
DISTRO=linux-x64
export PATH=/usr/local/lib/nodejs/node-$VERSION-$DISTRO/bin:$PATH
```

## 重新应用启动文件

```zsh
# bash
source ~/.profile

# zsh
source ~/.zshenv
```