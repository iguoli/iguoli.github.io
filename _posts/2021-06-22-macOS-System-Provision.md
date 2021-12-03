---
title: macOS System Provision
date: 2021-06-25
modify_date: 2021-11-06
tags: macOS
key: macOS-System-Provision-2021-06-25
---

## [使用恢复系统重新安装 macOS](https://support.apple.com/zh-cn/guide/mac-help/mchlp1599/11.0/mac/11.0)

重新启动 Mac，然后立即按住以下其中一个组合键

- 安装与电脑兼容的 macOS 最新版本：按住 **Option-Command-R**。
- 重新安装电脑原始版本的 macOS（包括可用的更新）：按住 **Shift-Option-Command-R**。
- 重新安装储存在电脑内建恢复宗卷中的 macOS 版本：按住 **Command-R**。

<!--more-->

## 使用优盘安装全新 macOS

### 准备 macOS 启动优盘

- 下载 macOS 安装包

```bash
git clone https://github.com/munki/macadmin-scripts.git
cd macadmin-scripts
./installinstallmacos.py
```

下载完成后会生成 macOS 系统的 dmg 文件。

- 点击 dmg 安装包，装载 macOS 系统安装文件。

- 将 macOS 系统安装文件写入到优盘, 参考[如何创建可引导的 macOS 安装器](https://support.apple.com/zh-cn/HT201372)

  - **Big Sur**

    ```bash
    sudo /Applications/Install\ macOS\ Big\ Sur.app/Contents/Resources/createinstallmedia --volume /Volumes/u_disk
    ```

  - **Catalina**

    ```bash
    sudo /Applications/Install\ macOS\ Catalina.app/Contents/Resources/createinstallmedia --volume /Volumes/u_disk
    ```

  - **Mojave**

    ```bash
    sudo /Applications/Install\ macOS\ Mojave.app/Contents/Resources/createinstallmedia --volume /Volumes/u_disk
    ```

如果是黑苹果，还要使用 [`Hackintool`](https://github.com/headkaze/Hackintool) 工具装载优盘的 **EFI** 分区，将制作好的 EFI 文件放入优盘 **EFI** 分区中，否则优盘无法引导黑苹果主机。
{:.warning}

### 优盘引导并安装

#### 白苹果

1. 插入优盘。
2. 将 Mac 开机或重启后，立即按住 **Option (Alt) ⌥** 键，直到出现可引导磁盘选项。
3. 选择可引导安装磁盘后回车。
4. 短暂等待后进入 Recovery 模式。

#### NUC8 黑苹果

1. 插入优盘。
2. 开机或重新启动后，立即持续点按 **F10**，进入 BIOS 启动菜单，选择 UEFI 开头的优盘进行引导。
3. 引导后立即按以下任意一种方法进入 Recovery 模式
    - 按住 **Command ⌘** 键，持续点按 **R** 键。
    - 持续点按 **ESC** 键或 **Option (Alt) ⌥** 键，出现 OC 引导菜单，按空格键，出现引导项 "macOS Boot From Recovery"，选中并回车。
4. 短暂等待后进入 Recovery 模式。

在进入 Recovery 模式后，首先选择 **磁盘工具** 来格式化硬盘。点击 **磁盘工具** 界面左上角的 **显示 -> 显示所有设备**，选中硬盘的根节点，然后点击界面上方的 **抹掉**，在弹出的抹掉对话框中填入和选择:

- 名称：Macintosh HD
- 格式：APFS
- 方案：GUID分区图

抹掉硬盘后，返回 Recovery 界面，选择 **重新安装 macOS**

 NUC8 黑苹果安装教程参考 weachy 大佬的 [NUC8（豆子峡谷）在线安装macOS，这才是OpenCore正确的打开方式](https://zhuanlan.zhihu.com/p/165608087)。

## 系统设置及常用工具安装

### 设置主机名

- 设置终端显示的主机名

```bash
sudo scutil --get HostName
sudo scutil --set HostName Lis-Macmini
```

- 设置局域网显示的主机名 (对应共享设置中的主机名)

```bash
sudo scutil --get ComputerName
sudo scutil --set ComputerName Lis-Macmini
```

### 开启长按按键连续输入

打开终端输入以下命令然后重启系统后生效。

```bash
defaults write NSGlobalDomain ApplePressAndHoldEnabled -boolean false
```

### 系统偏好设置

- **键盘**
  - **文本**：去掉 **自动纠正拼写** 等勾选。
  - **快捷键**：
    - Mojave系统：页面下方 **全键盘控制** 选择 **所有控制**。
    - Catalina系统：页面下方勾选 **使用键盘导航在控制间移动焦点**。
- **辅助功能** -> **鼠标与触控板** -> **触控板选项** -> 勾选 **启动拖移**，**三指拖移**

### 关闭系统完整性保护 SIP (System Integrity Protection)

1. 重启系统，系统开机时按住 **Command+R** 键进入 Recovery 模式。

2. 在 Recovery 界面顶部的 **Utilities** 菜单中打开终端。

3. 输入命令 `csrutil disable` 关闭 SIP (System Integrity Protection)。

4. 关闭终端，重启系统。

5. 进入系统后打开终端，输入命令 `csrutil status` 检查是否关闭。

### NUC8 黑苹果删除TF卡小图标

关闭 SIP 后，执行以下命令

```bash
sudo mount -uw / && killall Finder
rm -rf '/System/Library/CoreServices/Menu Extras/ExpressCard.menu'
touch '/System/Library/CoreServices/Menu Extras/ExpressCard.menu'
```

如需重新打开 SIP，则重启系统再次进入 Recovery 模式，在终端输入命令 `csrutil enable`。

### 科学上网

安装 [Surge] 或 [ClashX] 加速 GitHub 访问和外网工具下载。关于代理对网络的接管，可以参考 [Surge 官方手册] - [章节2 接管] 的解释:

[Surge]: https://nssurge.com/
[Surge 官方手册]: https://manual.nssurge.com/book/understanding-surge/cn/
[章节2 接管]: https://manual.nssurge.com/book/understanding-surge/cn/#代理服务接管方法-1
[ClashX]: https://github.com/yichengchen/clashX

> 要想使 Surge 实现后续的转发、修改和截获等功能，首先需要 Surge 对网络连接进行接管。
> 
> 在 macOS 和 iOS 下，要想使程序发出的网络连接被另一个程序所接管，而不是直接将数据发送到物理网卡，有以下三种方式：
>
> 1. 配置代理：如果系统配置了代理服务器，那么程序在执行网络请求的时候，就不会直接连接目标服务器，而是产生一个发向代理服务器的连接。利用这个特性，可以在本地启动一个代理服务，并配置系统代理为 127.0.0.1 （即本机）的一个端口，这样就可以接管网络请求。
>
> 
> <span style="color:tomato">但是，这种方式要求程序自身支持代理机制，系统的代理设置只是告知程序应该使用代理，需要程序自己完成代理的后续逻辑。好在，对于绝大部分带用户界面的程序，由于开发时使用了系统的高层网络框架（Cocoa/Cocoa Touch），开发者不需要进行任何额外的工作就可以支持代理。</span>
> 
> 而对于命令行程序，由于使用的是 POSIX 接口进行网络请求，该接口并没有对代理服务器提供内嵌支持，所以需要开发者自己完成对代理服务器的支持，这导致各种命令行程序对代理的支持情况和具体行为并不统一。同时由于大部分命令行程序并没有为 macOS 进行特殊处理，所以不会理会系统配置里的代理服务器设置。大部分命令行程序需要通过环境变量 https_proxy 和 http_proxy 去配置代理，还有一部分需要通过修改配置文件进行配置。
> 
> 还有少量程序由于完全缺乏代理服务器的支持，无法通过这种方式去接管网络连接。

### 设置命令行代理

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

### 安装 XCode Command Line Tools

XCode Command Line Tools 包含了 `git` 等系统常用的命令行工具。

```bash
xcode-select --install
```

### 安装 Homebrew

[Homebrew国内如何自动安装（国内地址）](https://zhuanlan.zhihu.com/p/111014448)

执行以下命令，脚本会提示选择国内几个比较常用的镜像，选择中科大镜像然后一路安装即可。

```bash
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"

# Set Homebrew-bottles mirror
export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles
```

#### 为 zsh 添加 Homebrew [自动补全](https://docs.brew.sh/Shell-Completion)

```bash
cat << EOF >> ~/.zprofile
# Add Homebrew autocompletion function to FPATH
FPATH="$(brew --prefix)/share/zsh/site-functions:${FPATH}"
EOF

# Rebuild zcompdump
rm -f ~/.zcompdump; compinit

# Eliminate "zsh compinit: insecure directories" warnings
chmod -R go-w "$(brew --prefix)/share"
```

### 安装 Connect 工具

- 从[Github下载](https://github.com/gotoh/ssh-connect)压缩包后直接解压即可，或

- 使用 Homebrew 安装

```bash
brew install connect
```

### 安装 [ProxyChains-NG](https://github.com/rofl0r/proxychains-ng)

```bash
brew install proxychains-ng

alias pcs='proxychains4'
```

```bash
$ vim $(brew --prefix)/etc/proxychains.conf

[ProxyList] 
socks5  127.0.0.1 7891
```

注意：对 `curl` 命令使用 `proxychains4` 代理，必须关闭系统完整性保护 (SIP)
{:.warning}

### 配置 Git 代理加速下载与 Clone

#### HTTP 和 HTTPS 协议代理

```bash
git config --global http.proxy 'socks5://127.0.0.1:7891'
git config --global https.proxy 'socks5://127.0.0.1:7891'
```

#### SSH 和 Git 协议代理

```bash
$ vim ~/.ssh/config
Host github.com
    User git
    # -S for socks5 proxy, -H for http proxy
    ProxyCommand connect -S 127.0.0.1:7891 %h %p
```

### 配置 [zsh](https://github.com/ohmyzsh/ohmyzsh)

```bash
echo $SHELL                           # 检查当前 shell
which zsh                             # 检查 zsh 是否安装
cat /etc/shells                       # 检查 zsh 是否已在 shell 列表
which zsh | sudo tee -a /etc/shells   # 如果 zsh 不在列表，将其写入到 shell 列表
chsh -s "$(which zsh)"                # 变更启动 shell 为 zsh
```

### 安装 [oh-my-zsh](https://github.com/ohmyzsh/ohmyzsh)

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

**注意**：如果因墙而无法访问，可以为 `curl` 命令[设置代理](#设置命令行代理)

#### 安装插件

[zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions)

```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

[zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting)

```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

### 安装 OpenJDK

- 从官方网站 [Adoptium][] (原网站[AdoptOpenJDK][]) 下载安装包直接安装。

- 或者使用 Homebrew 进行安装

```bash
brew search jdk

# 方法一
brew install openjdk@8
# 根据 Homebrew 提示，将 openjdk 链接到系统 Java wrappers
sudo ln -sfn $(brew --prefix)/opt/openjdk@8/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk-8.jdk

# 方法二
brew tap adoptopenjdk/openjdk
brew install --cask adoptopenjdk8
```

在 `~/.zshrc` 中设置 `JAVA_HOME` 环境变量

```bash
export JAVA_HOME=$(/usr/libexec/java_home -v 1.8)
```

[AdoptOpenJDK]: https://adoptopenjdk.net/
[Adoptium]: https://adoptium.net/

### 安装 MacVim 替代系统自带 Vim

```bash
brew install macvim
```

在 `/Applications` 文件夹创建应用链接

```bash
ln -Fs $(brew --prefix)/Cellar/macvim/<version>/MacVim.app /Applications/
```

MacVim 会在 `$(brew --prefix)/bin` 下创建 Vim 命令行程序的软链接，而系统自带的 Vim 在 `/usr/bin` 目录下，因此只要确保在 `$PATH` 环境变量中，`$(brew --prefix)/bin` 路径在 `/usr/bin` 之前即可使用新的 Vim。

#### 安装 [vim-plug](https://github.com/junegunn/vim-plug)

```bash
curl -fLo ~/.vim/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

### 安装 GNU 命令行工具

```bash
brew install coreutils findutils gnu-sed grep

export PATH="$(brew --prefix)/opt/grep/libexec/gnubin:$PATH"
export PATH="$(brew --prefix)/opt/gnu-sed/libexec/gnubin:$PATH"
export PATH="$(brew --prefix)/opt/findutils/libexec/gnubin:$PATH"
export PATH="$(brew --prefix)/opt/coreutils/libexec/gnubin:$PATH"
export MANPATH="$(brew --prefix)/opt/grep/libexec/gnuman:$MANPATH"
export MANPATH="$(brew --prefix)/opt/gnu-sed/libexec/gnuman:$MANPATH"
export MANPATH="$(brew --prefix)/opt/findutils/libexec/gnuman:$MANPATH"
export MANPATH="$(brew --prefix)/opt/coreutils/libexec/gnuman:$MANPATH"
```

### 安装 [Pygments](https://pygments.org)

Pygments 是代码高亮工具，用于美化输出。

```bash
brew install pygments

export LESSOPEN="|$(brew --prefix)/bin/pygmentize -g -O style=solarized-dark %s"
```

#### 样式

Pygments 提供了[内建样式](https://pygments.org/styles/)，下面的命令列出已安装的样式

```bash
pygmentize -L styles
```

### 安装 [Generic Colouriser](https://github.com/garabik/grc)

`grc` 工具可以彩色化其它命令行工具的输出

```bash
brew install grc
```

##### 自动设置常用命令别名

```bash
sed -e '$a\[[ -s "$(brew --prefix)/etc/grc.zsh" ]] && source $(brew --prefix)/etc/grc.zsh' ~/.zshrc
```

### 安装常用应用

- 终端应用 [iTerm2](https://iterm2.com/)
- 效率神器 [Alfred](https://www.alfredapp.com/)
- 改键神器 [Karabiner-Elements](https://karabiner-elements.pqrs.org/)
- 比对神器 [Beyond Compare](https://www.scootersoftware.com/)
- 卸载神器 [AppCleaner](https://freemacsoft.net/appcleaner/)
- 笔记工具 [Boostnote](https://github.com/BoostIO/Boostnote)
- 图标管理 [Bartender](https://www.macbartender.com/)
- KVM神器 [Barrier](https://github.com/debauchee/barrier)
- 亮屏工具 [Caffeinated](https://caffeinated.app)
- 日历工具 [小历](https://apps.apple.com/cn/app/小历-小而美的日历/id1114272557?mt=12)
- 显示器控制 [MonitorControl](https://github.com/MonitorControl/MonitorControl)
- 五笔输入法 [清歌输入法](https://qingg.im)
- 输入法指示 [ShowyEdge](https://showyedge.pqrs.org)
- 解压缩工具 [The Unarchiver](https://theunarchiver.com)
- 黑苹果工具 [Hackintool](https://github.com/headkaze/Hackintool)

### Beyond Compare

- 添加命令行 [Command Line Reference](https://www.scootersoftware.com/v4help/index.html?command_line_reference.html)

```bash
ln -s '/Applications/Beyond Compare.app/Contents/MacOS/bcomp' /usr/local/bin/
```
