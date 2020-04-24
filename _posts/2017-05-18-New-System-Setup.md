---
title: MacOS/Linux 新装系统环境配置
date: 2017-05-18
last_modified_at: 2020-04-23
categories: linux
---

## 安装系统常用开发工具

**Ubuntu**:

```zsh
sudo apt install -y build-essential
```

**Redhat**:

```zsh
sudo yum groupinstall "Development Tools"
```

## 安装[Git](https://github.com/git/git)

从 PPA 安装[ppa:git-core/ppa](https://launchpad.net/~git-core/+archive/ubuntu/ppa)

```zsh
sudo add-apt-repository ppa:git-core/ppa
sudo apt update
sudo apt install -y git
```

从源代码安装[git](https://github.com/git/git/blob/master/INSTALL)

```zsh
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

配置 git

```zsh
git config --global user.name 'your name'
git config --global user.email 'your@email.com'
git config --global credential.helper 'cache --timeout=86400'
git config --global http.proxy socks5://127.0.0.1:1080
git config --global https.proxy socks5://127.0.0.1:1080
git config --global core.editor vim
```

## 安装 [Connect](https://bitbucket.org/gotoh/connect/wiki/Home)

```zsh
sudo apt install connect-proxy

which connect
```

在 `.ssh/config` 中配置 `github` 使用代理

```zsh
$ vim ~/.ssh/config

Host github.com
    HostName github.com
    User git
    ProxyCommand connect -S socks-proxy:1080 %h %p
    ProxyCommand connect -H http-proxy:8080 %h %p
```

## 安装[Proxychains-ng](https://github.com/rofl0r/proxychains-ng)

`proxychains` 用于在命令行中为单个命令提供代理服务，这样可以在不启用全局代理的同时让某些命令通过代理连接。

注意: `proxychains` 只会代理 `TCP` 连接，所以如果使用 `proxychains4 ping www.google.com` 则不会生效，因为 `ping` 命令使用 `ICMP` 协议。

### Linux Distros

```zsh
sudo apt install -y build-essential
git clone https://github.com/rofl0r/proxychains-ng.git
cd proxychains-ng
./configure --prefix=/usr --sysconfdir=/etc
make
sudo make install
sudo make install-config (installs proxychains.conf)
```

配置文件: `/etc/proxychains.conf`

### MacOS

```zsh
brew install proxychains-ng
```

配置文件: `/usr/local/etc/proxychains.conf`

### 在配置文件中添加代理

在配置文件 [`proxychains.conf`](https://github.com/rofl0r/proxychains-ng/blob/master/src/proxychains.conf) 中的 `[ProxyList]` 中加入代理，通常只需要启用一个代理即可，多个代理会形成代理链。

```ini
strict_chain
proxy_dns
remote_dns_subnet 224
tcp_read_time_out 15000
tcp_connect_time_out 8000
[ProxyList]
socks5 127.0.0.1 1080
```

### 添加别名

```zsh
alias pcs=proxychains4
```

## 安装[zsh](https://github.com/zsh-users/zsh) and [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)

从源代码安装[zsh](https://github.com/zsh-users/zsh/blob/master/INSTALL)

```zsh
# for Ubuntu system
sudo apt install -y autoconf perl libncurses5-dev yodl
# for Redhat system
sudo yum install -y ncurses-devel

cd
git clone https://github.com/zsh-users/zsh.git
cd zsh
./Util/preconfig
./configure --prefix=/usr --bindir=/bin
make
sudo make install
which zsh | sudo tee -a /etc/shells
chsh -s "$(which zsh)"
```

登出当前 shell 后再重新登入，启用 zsh

安装[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)

```zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

安装[zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions)插件

```zsh
git clone https://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
```

安装[zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting)

```zsh
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

## 安装 Vim

从 PPA 安装[ppa:jonathonf/vim](https://launchpad.net/~jonathonf/+archive/ubuntu/vim)(Notice that this Vim don't have python compiled)

```zsh
sudo add-apt-repository ppa:jonathonf/vim
sudo apt update
sudo apt install -y vim
```

从源代码安装[Vim](https://github.com/Valloric/YouCompleteMe/wiki/Building-Vim-from-source)

```zsh
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

Mac 上安装 [MacVim](https://macvim-dev.github.io/macvim/)

- 使用 `brew install macvim` 命令安装，需要手工在 `/Applications` 文件夹创建应用链接

```zsh
ln -Fs /usr/local/Cellar/macvim/<version>/MacVim.app/Contents /Applications/MacVim.app
```

- 手工创建以下软链接，并确保 `$PATH` 环境变量中的 `/usr/local/bin` 在 `/usr/bin` 之前

```zsh
ln -s /Applications/MacVim.app/Contents/bin/mvim /usr/local/bin/vi
ln -s /Applications/MacVim.app/Contents/bin/mvim /usr/local/bin/vim
ln -s /Applications/MacVim.app/Contents/bin/mvim /usr/local/bin/vimdiff
ln -s /Applications/MacVim.app/Contents/bin/mvim /usr/local/bin/gvim
ln -s /Applications/MacVim.app/Contents/bin/mvim /usr/local/bin/gview
ln -s /Applications/MacVim.app/Contents/bin/mvim /usr/local/bin/gvimdiff
ln -s /Applications/MacVim.app/Contents/bin/mvim /usr/local/bin/mvim
ln -s /Applications/MacVim.app/Contents/bin/mvim /usr/local/bin/mview
ln -s /Applications/MacVim.app/Contents/bin/mvim /usr/local/bin/mvimdiff
ln -s /Applications/MacVim.app/Contents/bin/mvim /usr/local/bin/rvim
ln -s /Applications/MacVim.app/Contents/bin/mvim /usr/local/bin/view
```

安装[Vundle](https://github.com/VundleVim/Vundle.vim)

```zsh
git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim
cp ~/conf/.vimrc ~
vim +PluginInstall +qall
```

配置[YouCompleteMe](https://github.com/Valloric/YouCompleteMe)插件

```zsh
sudo apt-get install build-essential cmake python-dev python3-dev
cd ~/.vim/bundle/YouCompleteMe
./install.py --clang-completer
```

如果 VIM 里显示 YCM 启动不成功，有可能是 YCM 安装脚本自动下载的 LLVM 版本不对，可以参考[Full Installation Guide](https://github.com/Valloric/YouCompleteMe#full-installation-guide)，自己手动安装

- 从[LLVM](http://releases.llvm.org/download.html)官网下载最新版本的 Clang 压缩包并解压到`~/llvm`
- 编译 ycm_core 库

```zsh
cd ~
mkdir ycm_build
cd ycm_build
cmake -G "Unix Makefiles" . -DPATH_TO_LLVM_ROOT=~/llvm ~/.vim/bundle/YouCompleteMe/third_party/ycmd/cpp
cmake --build . --target ycm_core
```

## 安装[tmux](https://github.com/tmux/tmux)

```zsh
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

安装[oh-my-tmux](https://github.com/gpakosz/.tmux)

```zsh
cd
git clone https://github.com/gpakosz/.tmux.git
ln -s -f .tmux/.tmux.conf .
cp ~/conf/.tmux.conf.local .
```

## 安装常用工具

### 代码片段查询

- [howdoi](https://github.com/gleitz/howdoi)

- [cheat](https://github.com/cheat/cheat)

```zsh
brew install cheat
# or
pip install cheat

pip install howdoi
```

### 代码彩色化输出

[Pygments](https://pygments.org)

```zsh
pip install pygments

pygmentize hello.py
```

### 命令行彩色化输出

[Generic Colouriser](https://github.com/garabik/grc)

```zsh
brew install grc
apt install grc

grc diff
grc tail
grc ps
grc ping
```

## 安装 powerline fonts

参考[Powerline](https://github.com/powerline/fonts)

```zsh
# clone
git clone https://github.com/powerline/fonts.git --depth=1
# install
cd fonts
./install.sh
# clean-up a bit
cd ..
rm -rf fonts
```

## 安装[genpac](https://github.com/JinnLynn/genpac)，设置系统代理

```zsh
pip install genpac

# 生成autoproxy.pac文件
genpac -c ~/conf/genpac/config.ini

# 在LinuxMint下通过cinnamon-settings打开系统设置->网络->网络代理
# 使用自动设置，URL填写'file:///home/username/autoproxy.pac'
```

## 安装[google-chrome](https://www.google.com/chrome/browser/desktop/index.html)

从[google](https://www.google.com/chrome/browser/desktop/index.html)下载 deb 安装包
或者使用[google linux repository](https://www.google.com/linuxrepositories/)

```zsh
wget -q -O - https://dl.google.com/linux/linux_signing_key.pub | sudo apt-key add -
sudo sh -c "echo 'deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main' > /etc/apt/sources.list.d/google-chrome.list"
sudo apt update
sudo apt install -y google-chrome-stable
# 设置google通过pac代理上网
google-chrome --proxy-pac-url='file:///home/username/autoproxy.pac'
```