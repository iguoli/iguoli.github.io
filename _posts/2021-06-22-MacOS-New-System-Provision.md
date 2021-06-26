---
title: macOS New System Provision
date: 2021-06-25
modify_date: 2021-06-26
tags: macOS
key: macOS-New-System-Provision-2021-06-25
---

全新安装 macOS 系统及常用工具。

<!--more-->

## 安装 macOS

### 准备 macOS 启动优盘

- 下载 macOS 安装包

```zsh
git clone https://github.com/munki/macadmin-scripts.git
cd macadmin-scripts
./installinstallmacos.py
```

下载完成后会生成 macOS 系统的 dmg 文件。

- 点击 dmg 安装包，装载 macOS 系统安装文件。

- 将 macOS 系统安装文件写入到优盘, 参考[如何创建可引导的 macOS 安装器](https://support.apple.com/zh-cn/HT201372)

  - **Big Sur**

    ```zsh
    sudo /Applications/Install\ macOS\ Big\ Sur.app/Contents/Resources/createinstallmedia --volume /Volumes/u_disk
    ```

  - **Catalina**

    ```zsh
    sudo /Applications/Install\ macOS\ Catalina.app/Contents/Resources/createinstallmedia --volume /Volumes/u_disk
    ```

如果是黑苹果，还要使用 [`Hackintool`](https://github.com/headkaze/Hackintool) 工具装载优盘的 **EFI** 分区，将制作好的 EFI 文件放入优盘 **EFI** 分区中，否则优盘无法引导。
{:.warning}

### 优盘安装 macOS 系统

插入优盘重启机器，通过优盘引导进入 Recovery 模式。在进入 Recovery 模式后，首先选择 **磁盘工具** 来格式化硬盘。点击 **磁盘工具** 界面左上角的 **显示 -> 显示所有设备**，选中硬盘的根节点，然后点击界面上方的 **抹掉**，在弹出的抹掉对话框中填入和选择:

- 名称：Macintosh HD
- 格式：APFS
- 方案：GUID分区图

抹掉硬盘后，返回 Recovery 界面，选择 **重新安装 macOS**

 NUC8 黑苹果安装教程参考 weachy 大佬的 [NUC8（豆子峡谷）在线安装macOS，这才是OpenCore正确的打开方式](https://zhuanlan.zhihu.com/p/165608087)。

## 系统设置及常用工具安装

### 设置主机名

- 设置终端显示的主机名

```zsh
sudo scutil --get HostName
sudo scutil --set HostName Lis-Macmini
```

- 设置局域网显示的主机名 (对应共享设置中的主机名)

```zsh
sudo scutil --get ComputerName
sudo scutil --set ComputerName Lis-Macmini
```

### 开启长按按键连续输入

打开终端输入以下命令然后重启系统后生效。

```zsh
defaults write NSGlobalDomain ApplePressAndHoldEnabled -boolean false
```

### NUC8 黑苹果删除TF卡小图标

1. 重启系统，系统开机时按住 `Command+R` 键进入 Recovery 模式

2. 在 Recovery 界面顶部的 **Utilities** 菜单中打开终端

3. 输入命令 `csrutil disable` 关闭 SIP (System Integrity Protection)

4. 关闭终端，重启系统。

5. 进入系统后打开终端，输入以下命令

```zsh
sudo mount -uw / && killall Finder
rm -rf '/System/Library/CoreServices/Menu Extras/ExpressCard.menu'
touch '/System/Library/CoreServices/Menu Extras/ExpressCard.menu'
```

如需重新打开 SIP，则重启系统再次进入 Recovery 模式，在终端输入命令 `csrutil enable`

### 科学上网

安装 [ClashX](https://github.com/yichengchen/clashX) 加速 GitHub 访问和外网工具下载。

### 安装 XCode Command Line Tools

XCode Command Line Tools 包含了 `git` 等系统常用的命令行工具。

```zsh
xcode-select --install
```

### 配置 Git 代理加速下载与 Clone

#### HTTP 和 HTTPS 协议代理

```zsh
git config --global http.proxy 'socks5://127.0.0.1:7891'
git config --global https.proxy 'socks5://127.0.0.1:7891'
```

#### SSH 和 Git 协议代理

```zsh
$ vim ~/.ssh/config
Host github github.com
    HostName github.com
    Port 22
    User git
    IdentityFile ~/.ssh/li_rsa
    # -S for socks5 proxy, -H for http proxy
    ProxyCommand connect -S 127.0.0.1:7891 %h %p
```

`connect` 工具参考下面的 [安装 Connect 工具](#安装-connect-工具)

### 安装 Homebrew

[Homebrew国内如何自动安装（国内地址）](https://zhuanlan.zhihu.com/p/111014448)

执行以下命令，脚本会提示选择国内几个比较常用的镜像，选择中科大镜像然后一路安装即可。

```zsh
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"

# Set Homebrew-bottles mirror
export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles
```

### 安装 OpenJDK

- 从 [官方网站][AdoptOpenJDK] 下载安装包直接安装。

- 或者使用 Homebrew 进行安装

```zsh
brew search jdk

# 方法一
brew install openjdk@8
# 根据 Homebrew 提示，将 openjdk 链接到系统 Java wrappers
sudo ln -sfn /usr/local/opt/openjdk@8/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk-8.jdk

# 方法二
brew tap adoptopenjdk/openjdk
brew install --cask adoptopenjdk8
```

在 `~/.zshrc` 中设置 `JAVA_HOME` 环境变量

```zsh
export JAVA_HOME=$(/usr/libexec/java_home -v 1.8)
```

[AdoptOpenJDK]: https://adoptopenjdk.net/

### 安装 Connect 工具

```zsh
brew install connect
```

用法

```zsh
$ vim ~/.ssh/config
Host github.com
    User git
    HostName github.com
    ProxyCommand connect -S 127.0.0.1:7891 %h %p
```

### 安装 MacVim 替代系统自带 Vim

```zsh
brew install macvim
```

MacVim 会在 `/usr/local/bin` 下创建 Vim 命令行程序的软链接，而系统自带的 Vim 在 `/usr/bin` 目录下，因此只要确保在 `$PATH` 环境变量中，`/usr/local/bin` 路径在 `/usr/bin` 之前即可使用新的 Vim

### 安装 GNU 命令行工具

```zsh
brew install coreutils findutils gnu-sed grep

export PATH="/usr/local/opt/grep/libexec/gnubin:$PATH"
export PATH="/usr/local/opt/gnu-sed/libexec/gnubin:$PATH"
export PATH="/usr/local/opt/findutils/libexec/gnubin:$PATH"
export PATH="/usr/local/opt/coreutils/libexec/gnubin:$PATH"
export MANPATH="/usr/local/opt/grep/libexec/gnuman:$MANPATH"
export MANPATH="/usr/local/opt/gnu-sed/libexec/gnuman:$MANPATH"
export MANPATH="/usr/local/opt/findutils/libexec/gnuman:$MANPATH"
export MANPATH="/usr/local/opt/coreutils/libexec/gnuman:$MANPATH"
```

### 安装 [ProxyChains-NG](https://github.com/rofl0r/proxychains-ng)

proxychains-ng 是命令行代理工具。

```zsh
brew install proxychains-ng

alias pcs='proxychains4'
```

```zsh
$ vim /usr/local/etc/proxychains.conf

[ProxyList] 
socks5  127.0.0.1 7891
```

### 安装 [Pygments](https://pygments.org)

Pygments 是代码高亮工具，用于美化输出。

```zsh
brew install pygments

export LESSOPEN="|/usr/local/bin/pygmentize -g -O style=solarized-dark %s"
```

### 安装常用应用

- 效率神器 [Alfred](https://www.alfredapp.com/)
- 改键神器 [Karabiner-Elements](https://karabiner-elements.pqrs.org/)
- 比对神器 [Beyond Compare](https://www.scootersoftware.com/)
- 卸载神器 [AppCleaner](https://freemacsoft.net/appcleaner/)
- 笔记工具 [Boostnote](https://github.com/BoostIO/Boostnote)
- KVM神器 [Barrier](https://github.com/debauchee/barrier)
- 工具栏图标管理 [Bartender](https://www.macbartender.com/)
- 黑苹果工具 [Hackintool](https://github.com/headkaze/Hackintool)

### Beyond Compare

- [Command Line Reference](https://www.scootersoftware.com/v4help/index.html?command_line_reference.html)

```zsh
ln -s '/Applications/Beyond Compare.app/Contents/MacOS/bcomp' /usr/local/bin/
```
