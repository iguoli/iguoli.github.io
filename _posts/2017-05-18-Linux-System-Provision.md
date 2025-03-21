---
title: Linux System Provision
date: 2017-05-18
modify_date: 2022-05-30
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

<!--more-->

### Interactive login shell

当通过 text console 或 ssh 登录，或使用 `su -` 命令时，会得到一个 **interactive login** shell。当通过图形界面登录时 (例如 *X display manager*)，不会得到一个 login shell，而是得到一个 session manager 或 window manager.

### Non-interactive login shell

这种 shell 比较少见，一种情况是通过远程登录执行命令，将本地标准输入或标准输出传递给远程命令时会得到一个 **non-interactive login** shell。例如下面两个命令：

```sh
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

## 查看系统的 Linux 发行版

```sh
hostnamectl

cat /proc/version

uname -a

cat /etc/issue
```

### Ubuntu

```sh
lsb_release
```

### RedHat or CentOS

```sh
# Centos
cat /etc/centos-release

# RedHat
cat /etc/redhat-release
```

### Distributions Release file in */etc*

| Distributions | Release file in /etc                               |
| ------------- | -------------------------------------------------- |
| Red Hat       | `/etc/redhat-release`, `/etc/redhat_version`       |
| CentOS        | `/etc/centos-release`                              |
| Fedora        | `/etc/fedora-release`                              |
| Ubuntu        | `/etc/os-release`                                  |
| New Debian    | `/etc/os-release`                                  |
| Old Debian    | `/etc/debian_release`, `/etc/debian_version`       |
| Novell SuSE   | `/etc/SuSE-release`                                |
| Gentoo        | `/etc/gentoo-release`                              |
| Solaris/Sparc | `/etc/release`                                     |
| Slackware     | `/etc/slackware-release`, `/etc/slackware-version` |
| Mandrake      | `/etc/mandrake-release`                            |
| Yellow dog    | `/etc/yellowdog-release`                           |
| Sun JDS       | `/etc/sun-release`                                 |

## [update-alternatives](https://man7.org/linux/man-pages/man1/update-alternatives.1.html)

`update-alternatives` 管理系统中的软链接，用于切换软件的不同版本，这些软链接放置于 `/etc/alternatives` 目录下。

- 列出系统当前管理的命令软链接

```sh
# RedHat/CentOS
update-alternatives --list

# Debian/Ubuntu
update-alternatives --get-selections
```

- 将 python 3.8 版本加入到系统配置中

```sh
# --install <generic name> <symlink> <alternative> <priority>
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3.8 60
```

- 显示系统中存在的 java 版本

```sh
update-alternatives --display java
```

- 设置系统默认使用的 java 版本

```sh
update-alternatives --config java
```

- 设置系统默认使用的编辑器，例如选择 vim, nano

```sh
update-alternatives --config editor
```

- 将 python 3.8 从系统配置中删除

```sh
update-alternatives –-remove python /usr/bin/python3.8
```

## 用户及用户组操作

### 查看用户组

使用 `lid` (RedHat/CentOS) 或 `libuser-lid` (Ubuntu) 命令可以查看用户属于哪些组，或用户组包含哪些用户

**RedHat/CentOS**

```sh
sudo yum install libuser

lid [$USER]

lid -g groupname
```

**Ubuntu**

```sh
sudo apt install -y libuser

# 查看用户属于哪些组
libuser-lid [$USER]

# 查看 wheel 组中有哪些用户
libuser-lid -g groupname
```

下面两个命令只能查看用户属于哪些组

```sh
groups [$USER]

id [$USER]
```

### 将用户添加到用户组

将用户添加到 docker 用户组

```sh
usermod -aG docker $USER
```

### 从用户组中删除用户

从 docker 用户组中删除用户

```sh
gpasswd -d $USER docker
```

### 为系统添加新用户

```sh
# -m 生成用户目录，-G 添加用户到额外组
useradd -m -G wheel,docker $USER

# 设置用户密码
passwd $USER
```

### 变更用户名

下面一组命令用于变更用户名

```sh
usermod -l new-name old-name
mv /home/old-name /home/new-name
usermod -d /home/new-name new-name
usermod -c “description” new-name
groupmod -n new-group old-group
```

### 删除系统用户

```sh
# -r 会连同用户目录一起删除
userdel -r $USER
```

## 设置包管理器

### 设置代理

**Ubuntu APT**

```sh
$ vim /etc/apt/apt.conf.d/proxy.conf

Acquire {
  HTTP::proxy "http://10.1.1.122:7890";
  HTTPS::proxy "http://10.1.1.122:7890";
}
```

**RedHat/CentOS Yum/Dnf**

参考 [设置Yum仓库代理](https://iguoli.github.io/2016/08/02/DNF-YUM.html#设置-yum-仓库代理)

```sh
sudo dnf config-manager --setopt=proxy=http://127.0.0.1:7890 --save
```

### 加速安装包下载

```sh
sudo dnf config-manager --setopt=max_parallel_downloads=5 --setopt=fastestmirror=true --save
```

## 安装系统常用开发工具

**Ubuntu**

```sh
sudo apt install -y build-essential
```

**CentOS 8**

```sh
# 查看
sudo dnf groupinfo "Development Tools"

# 安装
sudo dnf group install "Development Tools"

# 卸载
sudo dnf group remove "Development Tools"
```

## 关闭 SELinux

用`getenforce`命令检查 SELinux 是否已禁用

```sh
getenforce
```

修改 `/etc/selinux/config` 配置文件，设置 SELinux 为 **disabled**

```sh
vim /etc/selinux/config
```

## 设置命令行代理

大部分终端命令都会识别以下环境变量来使用命令行代理，如果有特殊命令不识别，可以使用 **ProxyChains-NG** 命令。

```sh
export proxy_ip_port=localhost:7890
export http_proxy=http://$proxy_ip_port
export https_proxy=http://$proxy_ip_port
export all_proxy=socks5://$proxy_ip_port
```

取消命令行代理环境变量

```sh
unset all_proxy http_proxy https_proxy
```

## 安装 [Git](https://github.com/git/git)

**Ubuntu**

```sh
sudo apt install -y git
```

**RedHat/CentOS**

```sh
sudo yum install -y git
```

##### [从源代码安装](https://github.com/git/git/blob/master/INSTALL)

```sh
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

```sh
git config --global user.name 'your name'
git config --global user.email 'your@email.com'
git config --global credential.helper 'cache --timeout=86400'
git config --global http.proxy http://127.0.0.1:7890
git config --global https.proxy http://127.0.0.1:7890
git config --global core.editor nvim
```

## 安装 connect-proxy

**Ubuntu**

```sh
sudo apt install connect-proxy

# See the locations of the package installed
dpkg -L connect-proxy
```

**RedHat/CentOS**

```sh
sudo yum install connect-proxy

# See the locations of the package installed
rpm -ql connect-proxy
```

##### 配置

```sh
$ vim ~/.ssh/config

Host github.com
    User git
    # -S for socks5 proxy, -H for http proxy
    ProxyCommand connect-proxy -H 127.0.0.1:7890 %h %p
```

## 安装 [zsh](https://github.com/zsh-users/zsh)

**RedHat/CentOS**

```sh
sudo yum install -y zsh
```

##### [源代码安装](https://github.com/zsh-users/zsh/blob/master/INSTALL)

```sh
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

```sh
sudo yum install -y util-linux-user
which zsh | sudo tee -a /etc/shells
chsh -s "$(which zsh)"
```

登出当前 shell 后再重新登入，启用 zsh

## 安装 [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)

```sh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

### 安装插件

[zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions)

```sh
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

[zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting)

```sh
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

## 安装 [Neovim](https://github.com/neovim/neovim)

```sh
sudo dnf install -y neovim

git clone git@github.com:iguoli/nvim-config.git ~/.config/nvim
```

## 安装 [fzf](https://github.com/junegunn/fzf)

```sh
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf

~/.fzf/install
```

## 安装 [Pygments](https://pygments.org)

**PIP**

```sh
pip install pygments

pygmentize hello.py
```

**RedHat/CentOS**

```sh
sudo yum install -y python3-pygments

export LESSOPEN="|pygmentize -g -O style=colorful %s"
```

#### 样式

Pygments 提供了[内建样式](https://pygments.org/styles/)，下面的命令列出已安装的样式

```sh
pygmentize -L styles
```

## 安装 [Generic Colouriser](https://github.com/garabik/grc)

`grc` 工具可以彩色化其它命令行工具的输出

**Ubuntu**

```sh
apt install grc
```

**源码安装**

```sh
git clone https://github.com/garabik/grc.git
cd grc
./install.sh
cd ..
rm -rf grc

source /etc/grc.zsh
```

**自动设置常用命令别名**

```sh
sed -e '$a\[[ -s "/etc/grc.zsh" ]] && source /etc/grc.zsh' ~/.zshrc
```

## 安装 [Proxychains-ng](https://github.com/rofl0r/proxychains-ng)

`proxychains` 用于在命令行中为单个命令提供代理服务，这样可以在不启用全局代理的同时让某些命令通过代理连接。

注意: `proxychains` 只会代理 `TCP` 连接，所以如果使用 `proxychains4 ping www.google.com` 则不会生效，因为 `ping` 命令使用 `ICMP` 协议。

**RedHat/CentOS**

```sh
sudo yum install -y proxychains-ng
```
##### 源代码安装

```sh
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

```sh
alias pcs=proxychains4
```

## 安装 [Temurin](https://adoptium.net/) OpenJDK

[官方安装文档](https://adoptium.net/installation/linux)

安装包使用如下命名格式，

`temurin-<version>-jdk`

例如：`temurin-17-jdk`, `temurin-8-jdk`

首先，添加以下 RPM repo，如果用的是 Fedora，注意替换 `centos` 为 `fedora`

```sh
vim /etc/yum.repos.d/adoptium.repo
```

```conf
[Adoptium]
name=Adoptium
baseurl=https://packages.adoptium.net/artifactory/rpm/centos/\$releasever/\$basearch
enabled=1
gpgcheck=1
gpgkey=https://packages.adoptium.net/artifactory/api/gpg/key/public
```

```sh
sudo yum update
sudo yum install temurin-17-jdk
```

## 安装 [Powerline] fonts

[Powerline]: https://github.com/powerline/fonts

```sh
# clone
git clone https://github.com/powerline/fonts.git --depth=1
# install
cd fonts
./install.sh
# clean-up a bit
cd ..
rm -rf fonts
```
