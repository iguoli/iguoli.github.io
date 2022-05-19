---
title: Linux System Provision
date: 2017-05-18
modify_date: 2021-11-06
tags: Linux Provision
key: Linux-System-Provision-2017-05-18
---

## 不同的 Shell 类型

参考以下三个问题的回答:

- [Difference between Login Shell and Non-Login Shell?](https://unix.stackexchange.com/questions/38175/difference-between-login-shell-and-non-login-shell)
- [What is the difference between interactive shells, login shells, non-login shell and their use cases?](https://unix.stackexchange.com/questions/50665/what-is-the-difference-between-interactive-shells-login-shells-non-login-shell)
- [What should/shouldn't go in .zshenv, .zshrc, .zlogin, .zprofile, .zlogout?](https://unix.stackexchange.com/questions/71253/what-should-shouldnt-go-in-zshenv-zshrc-zlogin-zprofile-zlogout)

### Login shell

**Login** shell 是在用户登录后创建的第一个进程，该进程读取以下文件来设置环境变量：

- Bourne shell: `/etc/profile`, `~/.profile`
- Bash: `/etc/profile`, `~/.profile`, `~/.bash_profile`
- Zsh: `/etc/zprofile`, `~/.zprofile`
- Csh: `/etc/csh.login`, `~/.login`

之后其它的 shell 都是由这个 login shell 或其子孙 fork 出来，这些子孙 shell 会继承 login shell 的许多设置 (例如环境变量，`umask` 等)。

### Interactive login shell

当通过 text console 或 ssh 登录，或使用 `su -` 命令时，会得到一个 **interactive login** shell。当通过图形界面登录时 (例如 *X display manager*)，不会得到一个 login shell，而是得到一个 session manager 或 window manager.

### Non-interactive login shell

这种 shell 比较少见，一种情况是通过远程登录执行命令，将本地标准输入或标准输出传递给远程命令时会得到一个 **non-interactive login** shell。例如下面两个命令：

```bash
# 在远程主机执行本地脚本
ssh user@remote.net 'bash -s' < local-script.sh

# 将远程主机上的目录打包到本地
ssh user@remote.net 'tar czf - /opt/app' > app.tar.gz
```

### Interactive, non-login shell

当在一个已存在的 session (screen, X terminal, Emacs terminal buffer, a shell inside another, etc.) 的终端内打开一个 shell 时，就会得到一个 **interactive, non-login** shell。这个 shell 会读取下面的配置文件：

- Bash: `~/.bashrc`
- Zsh: `/etc/zshrc`, `~/.zshrc`
- Csh: `/etc/csh.cshrc`, `~/.cshrc`

### Non-interactive, non-login shell

当 shell 运行脚本或执行命令行命令时，会得到一个 **non-interactive, non-login** shell。这类 shell 非常常见，例如一个程序调用另一个程序，其实是在 shell 中运行一个小型脚本来调用另一个程序。这类 shell 会读取一个 *startup file*：

- Bash: `BASH_ENV` 环境变量指定的文件
- Zsh: `/etc/zshenv`, `~/.zshenv`

## 设置用户 sudo 权限

##### Ubuntu

更改系统默认编辑器为 vim

```bash
update-alternatives --config editor
```

使用 `sudo visudo` 命令会直接编辑系统默认配置，对于普通用户，更好的方法是在 `/etc/sudoers.d` 目录下单独添加配置文件，文件名可以使用用户名

```bash
cd /etc/sudoers.d
sudo visudo eric
```

在该文件中添加如下内容

```conf
# 不再需要用户 eric 输入密码
Defaults:eric      !authenticate

# 输入密码后 30 分钟内不需要再次输入密码
Defaults           timestamp_timeout=30
```

## 设置包管理器代理

##### Ubuntu Apt

```bash
$ vim /etc/apt/apt.conf.d/proxy.conf

Acquire {
  HTTP::proxy "http://10.1.1.122:7890";
  HTTPS::proxy "http://10.1.1.122:7890";
}
```

##### CentOS Yum/Dnf

参考 [设置Yum仓库代理](https://iguoli.github.io/2016/08/02/DNF-YUM.html#设置-yum-仓库代理)

## 安装系统常用开发工具

##### Ubuntu

```bash
sudo apt install -y build-essential
```

##### CentOS 8

```bash
sudo yum groupinstall "Development Tools"
```

<!--more-->

## 关闭 SELinux

用`getenforce`命令检查 SELinux 是否已禁用

```bash
getenforce
```

修改 `/etc/selinux/config` 配置文件，设置 SELinux 为 **disabled**

```bash
vim /etc/selinux/config
```

## 设置命令行代理

大部分终端命令都会识别以下环境变量来使用命令行代理，如果有特殊命令不识别，可以使用 **ProxyChains-NG** 命令。

```bash
export http_proxy=http://127.0.0.1:7890
export https_proxy=http://127.0.0.1:7890
export all_proxy=socks5://127.0.0.1:7891
```

取消命令行代理环境变量

```bash
unset all_proxy http_proxy https_proxy
```

## 安装 [Git](https://github.com/git/git)

##### Ubuntu

从 PPA 安装[ppa:git-core/ppa](https://launchpad.net/~git-core/+archive/ubuntu/ppa)

```bash
sudo add-apt-repository ppa:git-core/ppa
sudo apt update
sudo apt install -y git
```

##### CentOS 8

```bash
sudo yum install -y git
```

##### [从源代码安装](https://github.com/git/git/blob/master/INSTALL)

```bash
cd
sudo apt install -y build-essential autoconf asciidoc xsltproc
curl -L https://github.com/git/git/archive/master.zip -o git.zip
unzip git.zip
cd git-master
make configure
./configure --prefix=/usr
make all doc
sudo make install install-doc install-html
```

##### 配置

```bash
git config --global user.name 'your name'
git config --global user.email 'your@email.com'
git config --global credential.helper 'cache --timeout=86400'
git config --global http.proxy socks5://127.0.0.1:7891
git config --global https.proxy socks5://127.0.0.1:7891
git config --global core.editor vim
```

## 安装 [Connect](https://bitbucket.org/gotoh/connect/wiki/Home)

##### Ubuntu

```bash
sudo apt install connect-proxy

# See the locations of the package installed
dpkg -L connect-proxy
```

##### CentOS 8

```bash
sudo yum install connect-proxy

# See the locations of the package installed
rpm -ql connect-proxy
```

##### 配置

```bash
$ vim ~/.ssh/config

Host github.com
    User git
    # -S for socks5 proxy, -H for http proxy
    ProxyCommand connect-proxy -S 127.0.0.1:7891 %h %p
```

## 安装 [Proxychains-ng](https://github.com/rofl0r/proxychains-ng)

`proxychains` 用于在命令行中为单个命令提供代理服务，这样可以在不启用全局代理的同时让某些命令通过代理连接。

注意: `proxychains` 只会代理 `TCP` 连接，所以如果使用 `proxychains4 ping www.google.com` 则不会生效，因为 `ping` 命令使用 `ICMP` 协议。

##### CentOS 8 安装

```bash
sudo yum install -y proxychains-ng
```
##### 源代码安装

```bash
git clone https://github.com/rofl0r/proxychains-ng.git
cd proxychains-ng
./configure --prefix=/usr --sysconfdir=/etc
make
sudo make install
sudo make install-config (installs proxychains.conf)
```

##### 配置

在配置文件 [`/etc/proxychains.conf`](https://github.com/rofl0r/proxychains-ng/blob/master/src/proxychains.conf) 中的 `[ProxyList]` 中加入代理，通常只需要启用一个代理即可，多个代理会形成代理链。

```ini
[ProxyList]
socks5 127.0.0.1 7891
```

添加别名

```bash
alias pcs=proxychains4
```

## 安装 [zsh](https://github.com/zsh-users/zsh)

##### CentOS 8

```bash
sudo yum install -y zsh
```

##### [源代码安装](https://github.com/zsh-users/zsh/blob/master/INSTALL)

```bash
# for Ubuntu system
sudo apt install -y autoconf perl libncurses5-dev yodl
# for CentOS 8 system
sudo yum install -y ncurses-devel

cd
git clone https://github.com/zsh-users/zsh.git
cd zsh
./Util/preconfig
./configure --prefix=/usr --bindir=/bin
make
sudo make install
```

配置 zsh 为默认 shell

> CentOS 8 没有安装 `chsh` 工具，该工具位于 `util-linux-user` 安装包内，需要手工安装。

```bash
sudo yum install -y util-linux-user
which zsh | sudo tee -a /etc/shells
chsh -s "$(which zsh)"
```

登出当前 shell 后再重新登入，启用 zsh

## 安装 [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

### 安装插件

[zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions)

```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

[zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting)

```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

## 安装 Vim

##### Ubuntu

```bash
sudo apt install -y vim
```

##### CentOS 8

```bash
sudo yum install -y vim
```

##### [源代码安装](https://github.com/Valloric/YouCompleteMe/wiki/Building-Vim-from-source)

```bash
sudo apt install libncurses5-dev libgnome2-dev libgnomeui-dev \
libgtk2.0-dev libatk1.0-dev libbonoboui2-dev \
libcairo2-dev libx11-dev libxpm-dev libxt-dev python-dev \
python3-dev ruby-dev lua5.1 liblua5.1-dev libperl-dev git

sudo apt remove vim vim-runtime gvim
cd ~
git clone https://github.com/vim/vim.git
cd vim

./configure --with-features=huge \
            --enable-multibyte \
            --enable-rubyinterp=yes \
            --enable-python3interp=yes \
            --with-python3-config-dir=$(python3-config --configdir) \
            --enable-perlinterp=yes \
            --enable-luainterp=yes \
            --enable-gui=gtk2 \
            --enable-cscope \
            --prefix=/usr/local

make VIMRUNTIMEDIR=/usr/share/vim/vim80 # check the vim version before running

sudo make install
sudo update-alternatives --install /usr/bin/editor editor /usr/bin/vim 1
sudo update-alternatives --set editor /usr/bin/vim
sudo update-alternatives --install /usr/bin/vi vi /usr/bin/vim 1
sudo update-alternatives --set vi /usr/bin/vim
```

#### 安装 [vim-plug](https://github.com/junegunn/vim-plug)

```bash
curl -fLo ~/.vim/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

#### 安装 [YouCompleteMe](https://github.com/Valloric/YouCompleteMe) 插件

```bash
sudo apt-get install build-essential cmake python-dev python3-dev
cd ~/.vim/bundle/YouCompleteMe
./install.py --clang-completer
```

如果 VIM 里显示 YCM 启动不成功，有可能是 YCM 安装脚本自动下载的 LLVM 版本不对，可以参考[Full Installation Guide](https://github.com/Valloric/YouCompleteMe#full-installation-guide)，自己手动安装

- 从[LLVM](http://releases.llvm.org/download.html)官网下载最新版本的 Clang 压缩包并解压到`~/llvm`
- 编译 ycm_core 库

```bash
cd ~
mkdir ycm_build
cd ycm_build
cmake -G "Unix Makefiles" . -DPATH_TO_LLVM_ROOT=~/llvm ~/.vim/bundle/YouCompleteMe/third_party/ycmd/cpp
cmake --build . --target ycm_core
```

## 安装 [tmux](https://github.com/tmux/tmux)

```bash
# for Ubuntu system
sudo apt install -y libevent-dev libncurses5-dev autoconf automake pkg-config
# for Redhat system
sudo yum install -y libevent-devel ncurses-devel

$cd
git clone https://github.com/tmux/tmux.git
cd tmux
sh autogen.sh
./configure
make
sudo make install
```

### 安装 [oh-my-tmux](https://github.com/gpakosz/.tmux)

```bash
cd
git clone https://github.com/gpakosz/.tmux.git
ln -s -f .tmux/.tmux.conf .
cp ~/conf/.tmux.conf.local .
```

## 安装 [Pygments](https://pygments.org)

##### PIP

```bash
pip install pygments

pygmentize hello.py
```

##### CentOS 8

```bash
sudo yum install -y python3-pygments

export LESSOPEN="|pygmentize -g -O style=colorful %s"
```

#### 样式

Pygments 提供了[内建样式](https://pygments.org/styles/)，下面的命令列出已安装的样式

```bash
pygmentize -L styles
```

## 安装 [Generic Colouriser](https://github.com/garabik/grc)

`grc` 工具可以彩色化其它命令行工具的输出

##### Ubuntu

```bash
apt install grc
```

##### 源码安装

```bash
git clone https://github.com/garabik/grc.git
cd grc
./install.sh
cd ..
rm -rf grc

source /etc/grc.zsh
```

##### 自动设置常用命令别名

```bash
sed -e '$a\[[ -s "/etc/grc.zsh" ]] && source /etc/grc.zsh' ~/.zshrc
```

## 安装代码片段查询工具 [howdoi] 和 [cheat]

[howdoi]: https://github.com/gleitz/howdoi

[cheat]: https://github.com/cheat/cheat

```bash
pip install cheat

pip install howdoi
```

## 安装 [Powerline] fonts

[Powerline]: https://github.com/powerline/fonts

```bash
# clone
git clone https://github.com/powerline/fonts.git --depth=1
# install
cd fonts
./install.sh
# clean-up a bit
cd ..
rm -rf fonts
```
