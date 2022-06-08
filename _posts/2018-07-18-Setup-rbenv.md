---
title: rbenv - Ruby 版本管理工具
date: 2018-07-18
modify_date: 2021-12-16
tags: Ruby
key: Setup-rbenv-2018-07-18
---

使用 rbenv 管理系统中多个 Ruby 版本。

<!--more-->

## [安装 rbenv](https://github.com/rbenv/rbenv#basic-github-checkout)

```sh
# Clone rbenv into ~/.rbenv
git clone https://github.com/rbenv/rbenv.git ~/.rbenv
```

## 设置 rbenv 环境变量

```sh
# Optionally, try to compile dynamic bash extension to speed up rbenv. Don't worry if it fails; rbenv will still work normally
$ cd ~/.rbenv && src/configure && make -C src

$ vim ~/.zshrc
export PATH="$HOME/.rbenv/bin:$PATH"
eval "$(rbenv init -)"

# Verify that rbenv is properly set up using this rbenv-doctor script:
$ curl -fsSL https://github.com/rbenv/rbenv-installer/raw/master/bin/rbenv-doctor | bash
```

## [安装 ruby-build 插件](https://github.com/rbenv/ruby-build#readme)

```sh
# As an rbenv plugin
mkdir -p "$(rbenv root)"/plugins
git clone https://github.com/rbenv/ruby-build.git "$(rbenv root)"/plugins/ruby-build
```

## [安装 Ruby](https://github.com/rbenv/rbenv#installing-ruby-versions)

```sh
# list all available versions
rbenv install -l

# install a Ruby version
rbenv install 2.5.1

# list all installed Ruby versions
rbenv versions

# list current Ruby version
rbenv version

# set global version of Ruby to be used in all shells by writing the version name to the `~/.rbenv/version` file
rbenv global 2.5.1

# reports the currently configured global version
rbenv global

# set a local application-specific Ruby version by writing the version name to a .ruby-version file in the current directory
rbenv local 2.5.1

# set a shell-specific Ruby version
rbenv shell 2.5.1

# install Ruby gems
gem install bundler
```
