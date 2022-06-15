---
title: Git 常用命令
date: 2017-06-05
modify_date: 2021-04-17
tags: Git
key: Git-Commands-2017-06-05
---

## 查看 Git 自带文档

Git 帮助命令，以下三个命令结果相同

```sh
git help help

git help --help

man git-help
```

<!--more-->

所有可用 Git 命令帮助

```sh
git help --all

git help -a
```

所有可用的 Git 指南

```sh
git help --guides

git help -g
```

## Git 选项

`-c <name>=<value>`

传递配置参数给 Git 命令。覆盖配置文件中的参数值，`<name>` 的格式与 `git config` 命令中的格式相同。

- `git -c foo.bar`  
  配置参数不带等号 `=`，则设置 `foo.bar` 为 `true`

- `git -c foo.bar=`  
  配置参数带等号 `=` 但没有值，则设置 `foo.bar` 为空字符串，布尔类型的 (`git config --type=bool`) 转换为 `false`.

例如，关闭 `git log` 输出时的分页器 (默认是 `less`)

```sh
git -c pager.log= log -1

# 或关闭所有输出的分页器
git -c core.pager= log -1
```

## gitignore 文件

`gitignore` 文件指定哪些文件需要被 Git 忽略，`gitignore` 文件中的一行代表一个匹配模式。`Git` 会从多个来源读取 `gitignore` 模式，并按照下面从高到低的优先级顺序匹配.

- 从命令行读取的 `gitignore` 模式。

- 从仓库工作目录树的各层目录下的 `.gitignore` 文件中读取的模式。父目录的 `gitignore` 模式会被子目录中的 `gitignore` 模式覆盖。

  `.gitignore` 文件会被提交到仓库中，应该在此处添加项目中所有人都需要忽略的那些 `gitignore` 模式。
  {:.warning}

- 从仓库的 `$GIT_DIR/info/exclude` 文件中读取的模式。

  该文件不会被提交到仓库中，应该在此处添加不需要与其他人共享的 `gitignore` 模式。
  {:.warning}

- 从配置变量 `core.excludesfile` 所指定的文件中读取模式。

  对系统中的所有仓库都会生效，这个变量的默认值是 `$XDG_CONFIG_HOME/git/ignore`。
  {:.warning}

  ```sh
  git config --global core.excludesfile ~/.config/git/ignore
  ```

可以访问 [gitignore.io](https://www.gitignore.io/) 来自动创建各类常见的 `gitignore` 模式。

### 匹配模式的格式

```sh
# 所有目录下的所有 .gif 文件
*.gif

# 所有目录下的 photo.gif 文件
photo.gif

# Assets 目录及其子目录下的所有文件
Assets/

# Assets 目录下的所有文件，但不包括子目录
Assets/*

# Assets/Picture 目录下的所有 .gif 文件
Assets/Picture/*.gif

# 所有叫 Image 目录下的 .gif 文件
**/Image/*.gif

# 所有目录下名字包含 abc 的 .png 文件
*abc*.png
```

## gitattribute 文件

为每个路径定义相关属性，`gitattributes` 文件中每行的格式为:

```text
pattern attr1 attr2 [attribute list...]
```

属性有四种状态:

- **Set**  
  在属性列表中列出属性名，路径就会设置相应的属性为 `true`.

- **Unset**  
  在属性列表中的属性名前加上 `-`，路径就会设置相应的属性为 `false`.

- **Set to a value**  
  在属性列表中的属性名后跟一个 `=` 和对应的属性值，路径就会设置相应的属性为该属性值.

- **Unspecified**  
  无法说明路径有还是没有属性.

路径的模式匹配规则与 `.gitignore` 文件中的规则相同.

可以根据需要将属性放入以下不同文件:

- `$GIT_DIR/info/attributes` (仓库范围)  
  只想影响特定仓库.

- `.gitattributes` (网络范围)  
  对属性进行版本管理并扩散到其它仓库 (比如所有用户都感兴趣的属性).

- `core.attributesfile` (全局范围)  
  影响特定用户的所有仓库，默认值是 `$XDG_CONFIG_HOME/git/attributes`，如果 `$XDG_CONFIG_HOME` 没有设置或为空，则使用 `$HOME/.config/git/attributes` 文件.

- `$(prefix)/etc/gitattributes` (系统范围)  
  影响系统所有用户.

### [filter 属性](https://git-scm.com/book/zh/v2/自定义-Git-Git-属性)

一个非常有用的属性是 `filter` 属性，它的属性值是配置文件中定义的过滤器名. 过滤器由 `clean` 和 `smudge` 两个子过滤器组成。

`smudge` 过滤器在文件被检出时触发

![git attributes filter smudge](/assets/images/git-command/git-attr-filter-smudge.png)

`clean` 过滤器在文件被暂存时触发.

![git attributes filter clean](/assets/images/git-command/git-attr-filter-clean.png)

例如，你可以在 `.gitattributes` 文件中对 `*.c` 文件设置 `indent` 过滤器

```text
*.c filter=indent
```

然后在配置命令中定义 `indent` 过滤器

```sh
git config --global filter.indent.clean indent

git config --global filter.indent.smudge cat
```

也可以在 `.git/config` 文件中定义过滤器

```ini
[filter "indent"]
   clean = indent
   smudge = cat
```

## Git Config

`git config` 从四个文搜索配置项

- `GIT_DIR/config`  
  特定仓库配置文件，即本地范围 (--local) 配置文件.

- `~/.gitconfig`  
  特定用户配置文件，即全局范围 (--global) 配置文件.

- `$XDG_CONFIG_HOME/git/config`  
  特定用户第二配置文件。`$XDG_CONFIG_HOME` 没有设置或为空，则使用 `$HOME/.config/git/config` 文件.

- `$(prefix)/etc/gitconfig`  
  系统范围配置文件.

## [Git 内部原理 - Git 对象](https://git-scm.com/book/zh/v2/Git-内部原理-Git-对象)

### Git 对象

- 数据对象 (blob object)

- 树对象 (tree object)

- 提交对象 (commit object)

- 标签对象 (tag object)

这些 Git 对象存储于 `.git/objects` 目录下。

```sh
find .git/objects

find .git/objects -type f
```

底层命令 `hash-object` 可将任意数据保存于 .git 目录，并返回相应的键值。`-w` 选项指示 `hash-object` 命令存储数据对象；若不指定此选项，则该命令仅返回对应的键值。`--stdin` 选项则指示该命令从标准输入读取内容；若不指定此选项，则须在命令尾部给出待存储文件的路径。该命令输出一个长度为 40 个字符的校验和。这是一个 SHA-1 哈希值，一个将待存储的数据外加一个头部信息 (header) 一起做 SHA-1 校验运算而得的校验和。校验和的前两个字符用于命名子目录，余下的 38 个字符则用作文件名。

```sh
$ echo 'test content' | git hash-object -w --stdin
d670460b4b4aece5915caf5c68d12f560a9fe3e4

$ find .git/objects -type f
.git/objects/d6/70460b4b4aece5915caf5c68d12f560a9fe3e4
```

### 对象存储

以存储字符串 "what?" 为例，其被存储为 blob 对象，该数据对象的存储格式为 `头部信息 + 数据内容`。其中头部信息的格式为 `对象类型 + 空格 + 数据内容长度 + 空字节`。以 python 代码为例

```python
obj_type = 'blob'
content = 'what?'
length = len(content)               # 5

header = f'{obj_type} {length}\0'   # blob 5\x00

store = f'{header}{content}'        # blob 5\x00what?
```

查看 Git 对象

```sh
# 提交对象
git cat-file -p master

# 树对象
git cat-file -p master^{tree}

# 数据对象
git cat-file -p d67046

# 标签对象
git cat-file -p v1.1
```

### Git 对象关系

![Git 对象关系](/assets/images/git-command/git-objects.jpg)

### [Git 引用](https://git-scm.com/book/zh/v2/Git-内部原理-Git-引用)

Git 对象的文件名是一个 SHA-1 值，不便于记忆，因此我们需要一个文件来保存 SHA-1 值，并给文件起一个简单的名字，然后用这个名字指针来替代原始的 SHA-1 值。在 Git 里，这样的文件被称为 **引用** (**references**，或缩写为 **refs**)，你可以在 `.git/refs` 目录下找到这类含有 SHA-1 值的文件。

```sh
find .git/refs
```

```text
.git/refs
.git/refs/remotes
.git/refs/heads
.git/refs/tags
```

`git show-ref` 可以显示所有引用及其对应的 commit ID.

```sh
git show-ref

# 只显示 "refs/heads/ 和 "refs/tags" 目录下的引用
git show-ref --tags --heads

# -d, --dereference 将 tag 对象引用的 commit id 也显示出来，后面会跟一个 "^{}" 符号
git show-ref --tags -d
```

`git update-ref` 可以手动创建一个本地引用。比如创建一个名为 test 的引用，指向与 master 相同的提交对象。

```sh
git update-ref refs/heads/test `git rev-parse master`

git update-ref refs/heads/test 3739d69fc41b3b6f348104a9784d2a192171ce4d
```

**这基本就是 Git 分支的本质**，当运行类似于 `git branch test` 这样的命令时，Git 实际上会运行 update-ref 命令，取得当前所在分支最新提交对应的 SHA-1 值，并将其加入你想要创建的任何新引用中。

删除上面的 test 引用

```sh
git update-ref -d refs/heads/test
```

#### HEAD 引用

HEAD 文件是一个符号引用 (symbolic reference)，指向目前所在的分支。它不是指向一个 SHA-1 值，而是指向一个引用，可以使用 `git symbolic-ref` 来查看和修改符号引用。

```sh
# 查看 HEAD 指向的引用
$ cat .git/HEAD
ref: refs/heads/master

$ git symbolic-ref HEAD
refs/heads/master

# 修改 HEAD 指向的引用
$ git symbolic-ref HEAD refs/heads/test
$ cat .git/HEAD
ref: refs/heads/test
```

#### 标签引用

可以像这样创建轻量标签

```sh
git update-ref refs/tags/v1.0 `git rev-parse master`
```

#### 远程引用

远程引用作为记录远程仓库上各分支最后已知位置状态的书签，保存在 `refs/remotes` 目录下。如果添加一个叫做 origin 的远程仓库，然后把 master 分支推送过去，就会有一个 `refs/remotes/origin/master` 文件，记录最后一次与远程仓库通信时，远程 master 分支所对应的 SHA-1 值。

### [包文件](https://git-scm.com/book/zh/v2/Git-内部原理-包文件)

Git 最初向磁盘中存储对象时所使用的格式被称为"**松散 (loose)**"对象格式。但是，Git 会时不时地将多个这些对象打包成一个称为"**包文件 (packfile)**"的二进制文件，以节省空间和提高效率。当版本库中有太多的松散对象，或者你手动执行 `git gc` 命令，或者你向远程服务器执行推送时，Git 都会这样做。

```sh
git gc
```

这个时候再查看 objects 目录，你会发现大部分的对象都不见了，与此同时出现了一对新文件: 创建的包文件和一个索引。

```sh
$ find .git/objects -type f
.git/objects/bd/9dbf5aae1a3862dd1526723246b20206e5fc37
.git/objects/d6/70460b4b4aece5915caf5c68d12f560a9fe3e4
.git/objects/info/packs
.git/objects/pack/pack-978e03944f5c581011e6998cd0e9e30000905586.idx
.git/objects/pack/pack-978e03944f5c581011e6998cd0e9e30000905586.pack
```

`git verify-pack -v` 这个底层命令可以让你查看已打包的内容。

```sh
git verify-pack -v .git/objects/pack/pack-978e03944f5c581011e6998cd0e9e30000905586.idx
```

### [引用规格](https://git-scm.com/book/zh/v2/Git-内部原理-引用规格)

假设你添加了这样一个远程仓库:

```sh
git remote add origin https://github.com/schacon/simplegit-progit
```

上述命令会在你的 `.git/config` 文件中添加一个小节，并在其中指定远程版本库的名称 (origin)、URL 和一个用于 `fetch` 操作的**引用规格 (refspec)**

```sh
[remote "origin"]
   url = https://github.com/schacon/simplegit-progit
   fetch = +refs/heads/*:refs/remotes/origin/*
```

<span style="color:red"><b>引用规格的格式</b></span> = `[+]<src>:<dst>`。`<src>` 代表远程仓库中引用；`<dst>` 代表远程引用在本地的位置；`+` 表示即使 Git 在不能快进的情况下也要强制更新引用。

默认情况下，引用规格由 `git remote add` 命令自动生成，Git 获取服务器中 `refs/heads/` 下面的所有引用，并将它写入到本地的 `refs/remotes/origin/` 中。所以，如果服务器上有一个 master 分支，我们可以在本地通过下面这种方式来访问该分支上的提交记录:

```sh
git log origin/master
git log remotes/origin/master
git log refs/remotes/origin/master
```

上面的三个命令作用相同，因为 Git 会把它们都扩展成 `refs/remotes/origin/master`。

可以使用 <span style="color:red"><b>命名空间 (或目录) </b></span>来对引用进行分类管理。假设你有一个 QA 团队，他们推送了一系列分支，同时你只想要获取 `master` 和 QA 团队的所有分支而不关心其他任何分支，那么可以使用如下配置:

```sh
[remote "origin"]
   url = https://github.com/schacon/simplegit-progit
   fetch = +refs/heads/master:refs/remotes/origin/master
   fetch = +refs/heads/qa/*:refs/remotes/origin/qa/*
```

类似的，你可以为开发团队，集成团队创建各自的命名空间，非常方便。

#### 引用规格推送

QA 团队最初应该如何将他们的分支放入远程的 `qa/` 命名空间呢？我们可以通过**引用规格推送**来完成这个任务。

```sh
git push origin master:refs/heads/qa/master
```

如果他们希望 Git 每次运行 `git push origin` 时都像上面这样推送，可以在他们的配置文件中添加一条 `push` 值:

```sh
[remote "origin"]
   url = https://github.com/schacon/simplegit-progit
   fetch = +refs/heads/*:refs/remotes/origin/*
   push = refs/heads/master:refs/heads/qa/master
```

这会让 `git push origin` 默认把本地 `master` 分支推送到远程 `qa/master` 分支。

#### 删除引用

可以通过引用规格从远程服务器上删除引用

```sh
git push origin :topic
```

因为引用规格的格式是 `<src>:<dst>`，所以上述命令把 `<src>` 留空，意味着把远程版本库的 `topic` 分支定义为空值，也就是删除它。

### [维护与数据恢复](https://git-scm.com/book/zh/v2/Git-内部原理-维护与数据恢复)

Git 会不定时地自动运行一个叫做 "`auto gc`" 的命令，这个命令会做以下事情:

- 收集所有松散对象并将它们放置到包文件中
- 将多个包文件合并为一个大的包文件
- 移除与任何提交都不相关的陈旧对象。

`gc` 会做的另一件事是打包你的引用到一个单独的文件 `.git/packed-refs`

```sh
$ git gc
$ cat .git/packed-refs
# pack-refs with: peeled fully-peeled
cac0cab538b970a37ea1e769cbbde608743bc96d refs/heads/experiment
ab1afef80fac8e34258ff41fc1b867c702daa24b refs/heads/master
cac0cab538b970a37ea1e769cbbde608743bc96d refs/tags/v1.0
9585191f37f7b0fb9444f35a9bf50de191beadc2 refs/tags/v1.1
^1a410efbd13591db07496601ebc7a059dd55cfe9
```

`.git/packed-refs` 文件中，以 `^` 开头的记录，表示它上一行的标签是附注标签，`^` 所在的那一行是附注标签指向的提交对象。

`git count-objects -v` 命令来快速查看数据库占用空间大小。

```sh
git count-objects -v
```

## [选择修订版本](https://git-scm.com/book/zh/v2/Git-工具-选择修订版本)

查看 gitrevisions 了解 revision 相关概念

```sh
man gitrevisions
```

### 简短的 SHA-1

Git 可以为 SHA-1 值生成出简短且唯一的缩写。`git log` 的 `--abbrev-commit` 参数会显示简短且唯一的 SHA-1 值，默认是 7 个字符。

```sh
git log --abbrev-commit --pretty=oneline
```

### 分支引用

如果你想知道某个分支指向哪个特定的 SHA-1，或者想看任何一个例子中被简写的 SHA-1 ，你可以使用一个叫做 `rev-parse` 的 Git 探测工具。

```sh
git rev-parse master
git rev-parse origin/master
git rev-parse v1.1
```

### 引用日志

Git 会在后台保存一个引用日志(reflog)， 记录最近几个月你的 HEAD 和分支引用所指向的历史，
包括撤销掉的提交，类似于 bash 的 history 功能。 引用日志只存在于本地仓库，只记录你自己的操作记录。

```sh
git reflog

# 使用@{n}来引用reflog的提交记录
git show HEAD@{1}

# 查看某个时间的提交记录，具体格式查看gitrevisions
git show master@{yesterday}

# -g/--walk-reflogs 选项以标准日志的格式输出引用日志
git log -g
```

### 祖先引用

`rev^[n]`: 适用于有多个父提交的场景，如 git merge 后有多个父提交

- `HEAD^`: 相当于`HEAD^1`，指向该对象的第一父提交，
- `HEAD^2`: 指向该对象的第二父提交

`rev~[n]`: 指向该对象的第 n 代祖先提交

- `HEAD~`: 相当于`HEAD^`，指向该对象的第一父提交
- `HEAD~2`: 相当于`HEAD^^`，指向该对象的第一父提交的父提交

图例说明

```text
   G   H   I   J
    \ /     \ /
     D   E   F
      \  |  / \
       \ | /   |
        \|/    |
         B     C
          \   /
           \ /
            A

A =      = A^0
B = A^   = A^1     = A~1
C = A^2  = A^2
D = A^^  = A^1^1   = A~2
E = B^2  = A^^2
F = B^3  = A^^3
G = A^^^ = A^1^1^1 = A~3
H = D^2  = B^^2    = A^^^2  = A~2^2
I = F^   = B^3^    = A^^3^
J = F^2  = B^3^2   = A^^3^2
```

### 提交区间

#### 双点

最常用的提交区间语法是**双点**。这种语法可以让 Git 选出在一个分支中而不在另一个分支中的提交。比如，你想要查看 experiment 分支中还有哪些提交尚未被合并入 master 分支。你可以使用 `master..experiment` 来让 Git 显示 <span style="color:red">"在 experiment 分支中而不在 master 分支中的提交"</span>。

```sh
git log master..experiment
```

你也可以反过来查看在 master 分支中而不在 experiment 分支中的提交

```sh
git log experiment..master
```

#### 多点

Git 允许你在任意引用前加上 ^ 字符或者 --not 来指明你不希望提交被包含其中的分支。因此下列3个命令是等价的:

```sh
git log refA..refB
git log refB ^refA
git log refB --not refA
```

你可以在查询中指定超过两个的引用，这是双点语法无法实现的。比如，你想查看所有被 refA 或 refB 包含的但是不被 refC 包含的提交，你可以输入下面中的任意一个命令

```sh
git log refA refB ^refC
git log refA refB --not refC
```

#### 三点

这个语法可以选择出被两个引用中的一个包含但又不被两者同时包含的提交。`--left-right` 参数会显示每个提交到底处于哪一侧的分支。

```sh
git log --left-right master...experiment
```

![double dots vs. triple dots](/assets/images/git-command/double-triple-dots.png)

## 差异比较

```sh
# 比较working tree和index tree的不同
git diff

# 比较index tree和commit对象的不同，不写commit对象则默认用HEAD引用
git diff --staged master
git diff --cached

# 比较working tree和commit对象的不同
git diff HEAD

# 比较working tree与commit对象中test文件的不同
git diff HEAD -- ./test

# 比较两个commit对象
git diff HEAD HEAD^
git diff HEAD origin/master
```

## [重置揭密](https://git-scm.com/book/zh/v2/Git-工具-重置揭密)

```sh
# 1. 移动HEAD(--soft)
git reset --soft HEAD~

# 2. 更新索引(--mixed)
git reset
git reset --mixed HEAD

# 3. 更新工作目录(--hard)
git reset --hard [HEAD]

# git reset命令默认是--mixed, 所以下面命令等同于前两步命令
git reset HEAD~

# 这三步命令等同于
git reset --hard HEAD~

# 指定文件路径的撤销，注意指定路径只能作用于索引更新
# 无法作用于工作目录更新
git reset -- filename
git reset HEAD~ -- filename

# 下面命令无法执行
git reset --hard -- filename

# 与上面等价的命令，会同时更新索引和工作目录
git checkout HEAD -- filename
git checkout HEAD~2 -- filename
```

## [撤销操作](https://git-scm.com/book/zh/v2/Git-基础-撤消操作)

对于本地已经提交，还未推送远端仓库的提交，可以在本地进行撤销操作。

```sh
# 用新的提交来替换最近一次提交
# 如果文件没有变化，则只修改提交信息
git commit --amend -m '新的提交替换上次提交'

# 上面的命令等同于
git reset --soft HEAD~
git commit -m '新的提交'
```

## [回滚操作](https://git-scm.com/docs/git-revert)

对于已经推送远端仓库的提交，可以将已经存在的一个或多个提交的变更回滚掉，并记录一个新的提交。
回滚操作相当于 git 替你将之前的变更删掉，再将老的代码粘贴回来，将这些变更再重新提交。

```sh
# 回滚HEAD中倒数第4个提交的变更，并生成一个新的提交
git revert HEAD~3

# 回滚master中从倒数第5个提交（包含）到倒数第3个提交（包含）所做的变更，但不创建新的提交，只回滚工作目录及index
# 注意master~5是倒数第6个提交，但master~5..master~2不包含倒数第6个提交的变更
git revert -n master~5..master~2

```

## 变基操作

```sh
# 基本命令形式
git rebase <upstream> [branch]

# 例如
git rebase master
git rebase master develop
```

如果指定了`<branch>`，在变基前会先进行`git checkout <branch>`的动作，没有指定的话就是操作当前`<branch>`，因此上例中的第二个命令形式等价于

```sh
git checkout develop
git rebase master
```

变基的步骤：

1. 将所有在当前分支但不在`<upstream>`分支的提交保存到一个临时区域，这部分提交相当于`git log <upstream>..HEAD`或`git log 'fork_point'..HEAD`看到的内容；
2. 将当前分支重置为`<upstream>`，这相当于`git reset --hard <upstream>`；
3. 将之前保存在临时区域的提交再按顺序一个一个的应用到当前分支。注意，任何有相同修改只是提交信息或提交时间不一样的提交都会被忽略。

## 暂存工作，更新仓库，恢复工作

```sh
git stash # 工作区修改暂存
git pull  # 更新分支
git stash pop # 暂存修改恢复到工作区
```

## [远程仓库](https://git-scm.com/book/zh/v2/Git-基础-远程仓库的使用)

```sh
# 查看已经配置的远程仓库
git remote -v
git remote -vv

# 列出远程引用
git ls-remote [origin]

# 列出远程引用详细信息
git remote show [remote-name]
git remote show origin
git remote show dev

# 添加一个新的远程引用
git remote add <shortname> <url>
git remote add dev https://github.com/user/repo.git

# 修改已有远程引用的URL
git remote set-url dev git@github.com:user/repo.git

# 重命名远程仓库
git remote rename dev alpha

# 删除远程仓库
git remote rm alpha
```

## [Git 分支](https://git-scm.com/book/zh/v2/Git-分支-分支简介)

创建分支

```sh
git branch hotfix
```

切换分支

```sh
git checkout hotfix
```

创建并切换分支，等价于执行上面两条命令

```sh
git checkout -b hotfix
```

将 hotfix 分支合并到 master 分支

```sh
git checkout master
git merge hotfix
```

删除 hotfix 分支

```sh
git branch -d hotfix
```

`git branch --force branchname startpoint` 可以将 `branchname` 分支强制重置为 `startpoint` 所在的 commit，因此可以用来快速移动分支，例如下面的命令就是将 master 移动到与 develop 分支相同的 commit.

```sh
git branch -f master develop
```

列出所有分支

```sh
git branch

# 显示分支引用的 commit 对象及 commit 信息
git branch -v

# 显示分支引用的 commit 对象及 commit 信息以及跟踪的远程分支
git branch -vv

# 列出已经合并到当前分支的分支，这些分支可以删掉，因为其内容已经合并到了当前分支
git branch --merged

# 列出还没有与当前分支合并过的分支
git branch --no-merged
```

### [远程分支](https://git-scm.com/book/zh/v2/Git-分支-远程分支)

Stack Overflow 上关于 [What are the differences between local branch, local tracking branch, remote branch and remote tracking branch?](https://stackoverflow.com/questions/16408300/what-are-the-differences-between-local-branch-local-tracking-branch-remote-bra) 的回答

- A **local branch** is a branch that only you (the local user) can see. It exists only on your local machine.

- A **local tracking branch** is a local branch that is tracking another branch. This is so that you can push/pull commits to/from the other branch. Local tracking branches in most cases track a remote tracking branch.

- A **remote tracking branch** is a local copy of a remote branch.

- A **remote branch** is a branch on a remote location (in most cases `origin`).

![Git braches](/assets/images/git-command/git-branches.jpg)

<span style="color:red">远程引用</span>是对远程仓库的引用（指针），包括分支、标签等等。**远程跟踪分支**是远程分支状态的引用，远程跟踪分支以 **_remote/branch_** 的形式命名。

列出所有的**远程引用**

```sh
git ls-remote
```

列出所有**远程跟踪分支**

```sh
git branch -r
```

列出**远程分支**的详细信息

```sh
git remote show origin
```

抓取远程分支数据，更新远程跟踪分支。抓取哪些远程分支由 `git config remote.origin.fetch` 的[引用规格](#引用规格)给出。假如有 `origin`，`stage` 和 `production` 三个远程仓库

```sh
# 抓取默认的远程仓库上的分支 (origin)
git fetch

# 抓取 production 上的分支数据
git fetch production

# 抓取所有远程仓库上的分支，本例即抓取 origin, stage, production 三个远程仓库
git fetch --all

# 抓取所有远程仓库上的分支，对远程仓库上已经不存在的分支，删除对应的远程跟踪分支
git fetch --all --prune
```

删除远程跟踪分支

```sh
git branch -r -d origin/features
```

将远程跟踪分支数据合并到当前分支

```sh
git merge origin/master
```

从一个远程跟踪分支检出一个本地分支会自动创建所谓的 "**跟踪分支 (tracking branch)**"，它跟踪的分支叫做 "**上游分支 (upstream branch)**"。如果在一个跟踪分支上输入 `git pull`，Git 能自动地识别去哪个服务器上抓取、合并到哪个分支。

```sh
git checkout -b feature-1 origin/feature-1
```

由于这个命令十分常用，Git 提供了 `-t 或 --track` 选项来简化操作

```sh
git checkout -t origin/feature-1
```

设置已有的本地分支跟踪一个刚刚拉取下来的远程分支，或者想要修改正在跟踪的上游分支，你可以使用 `-u 或 --set-upstream-to` 选项运行 git branch 来显式地设置。

```sh
git branch -u origin/dev
```

查看设置的所有跟踪分支

```sh
git branch -vv
```

### Git Prune, Git Fetch --prune, Git Remote Prune 的区别

`git prune` 命令是一个内部清理程序，用于清除无法访问或"孤立"的 Git 对象。无法访问的对象是任何引用都无法到达的对象，任何无法通过分支或标记访问的提交都被视为无法访问。`git prune` 一般不直接执行，它被认为是垃圾收集命令，是 `git gc` 命令的子命令。

`git remote prune` 和 `git fetch --prune` 做同样的事情：删除已经不存在的远程分支的引用。这在团队工作流中非常需要，远程分支在合并到 `master` 之后会被删除。第二个命令 `git fetch --prune` 在 prune 远程引用之前先连接到远程仓库获取最新的状态。 它本质上是下面命令的组合:

```sh
git fetch --all && git remote prune
```

## 推送

**repository**: 远程仓库，可以是 URL 或远程引用，默认为 **origin**；

**refspecs**: `src:dest`, src 可以是分支名，也可以是任意SHA-1表达式 (参考 gitrevisions)，推送空的 src 可以从远程仓库删除对应的分支；

**push.default**

- simple:   推送本地当前分支到远程同名分支，如果没有同名远程分支，则推送中止。
- matching: 推送本地所有分支到远程同名分支。

```sh
git push [options] [<repository> [<refspecs>...]]

# 测试推送结果，但不真的推送
git push --dry-run origin local-branch
git push -n origin local-branch

# 推送到与本地分支同名的远程分支，如果没有找到，则创建一个同名远程分支
git push origin local-branch

# 推送到不同名的远程分支
git push origin local-branch:remote-branch

# 推送到远程分支并设置为跟踪分支
git push -u origin local-branch
git push -u origin local-branch:remote-branch

# 从服务器删除远程分支
git push origin --delete remote-branch
git push origin :remote-branch

# 删除远程标签
git push origin :/refs/tags/v1.1
```

## [打标签](https://git-scm.com/book/zh/v2/Git-基础-打标签)

### 列出已有标签

```sh
$ git tag
v0.1
v1.0
v1.1
v2.3
```

通过模式搜索标签

```sh
$ git tag -l 'v1.*'
v1.0
v1.1
```

### Git 标签类型

- 轻量级标签 (lightweight tags): 指向特定提交对象 (commit object) 的引用

- 含附注的标签 (annotated tags) :是一个标签对象 (tag object)，有自己的 SHA-1 信息，包含着标签的名字，电子邮件地址和日期，以及标签说明，一般建议使用含附注型的标签，以便保留相关信息。

### 含附注标签 (annotated tags)

创建含附注型标签

```sh
# -a 表示 annotated，-m 指定标签说明信息
git tag -a v1.4 -m 'my version 1.4'
```

可以对任意存在的提交对象 (commit object) 打上标签

```sh
git tag -a v1.3 -m 'last commit' HEAD^

git tag -a v1.2 -m 'version 1.2' 9fceb02
```

查看指定标签

```sh
git show v1.4
```

### 轻量级标签 (lightweight tags)

轻量级标签实际上就是一个保存着对应提交对象的校验和信息的文件。创建轻量级标签不需要任何参数，直接给出标签名字即可

```sh
git tag v1.4-lw
```

### 推送标签

默认情况下，`git push` 并不会把标签传送到远端服务器上，只有通过显式命令才能分享标签到远端仓库。其命令格式如同推送分支，运行 `git push origin [tagname]` 即可

```sh
git push origin v1.5
```

如果要一次推送所有本地新增的标签上去，可以使用 --tags 选项

```sh
git push origin --tags
```

#### 同时推送 commits 和 tag

```sh
# git push --atomic <branch> <tag>
git push --atomic master v1.5
```

还可以使用 `--follow-tags` 选项，不过其只推送 annotation tags，所有的 lightweight tags 不会被推送，并且只推送与要提交的 commits 相关联的那些标签。

```sh
git push --follow-tags
```

可以将 `--follow-tags` 设置成系统默认选项

```sh
git config --global push.followTags true
```

### 删除标签

使用 `git tag -d <tagname>` 可以删除本地标签

```sh
git tag -d v1.4-lw
```

使用 `git push origin :refs/tags/v1.4-lw` 删除远程仓库中的标签

```sh
git push origin :refs/tags/v1.4-lw
```

## [在服务器上搭建 Git](https://git-scm.com/book/zh/v2/服务器上的-Git-在服务器上搭建-Git)

使用 `--bare` 选项来克隆一个裸仓库，即一个不包含当前工作目录的仓库。

```sh
git clone --bare https://github.com/user/my-project.git my-project.git
```

Stack Overflow 上一篇关于 [`Git clone --mirror` 和 `Git clone --bare` 有什么不同](https://stackoverflow.com/questions/3959924/whats-the-difference-between-git-clone-mirror-and-git-clone-bare) 的回答

`man git-clone` 中的定义

> --bare
>
> Make a bare Git repository. That is, instead of creating `<directory>` and placing the administrative files in `<directory>/.git`, make the `<directory>` itself the `$GIT_DIR`. This obviously implies the -n because there is nowhere to check out the working tree. Also the branch heads at the remote are copied directly to corresponding local branch heads, without mapping them to `refs/remotes/origin/`. When this option is used, neither remote-tracking branches nor the related configuration variables are created.

> --mirror
>
> Set up a mirror of the source repository. This implies --bare. Compared to --bare, --mirror not only maps local branches of the source to local branches of the target, it maps all refs (including remote branches, notes etc.) and sets up a refspec configuration such that all these refs are overwritten by a git remote update in the target repository.

Suppose origin has a few branches (`master (HEAD)`, `next`, `pu`, and `maint`), some tags (`v1`, `v2`, `v3`), some remote branches (`devA/master`, `devB/master`), and some other refs (`refs/foo/bar`, `refs/foo/baz`, which might be notes, stashes, other devs' namespaces, who knows).

- `git clone origin-url` (**non-bare**): You will get all of the tags copied, a local branch `master (HEAD)` tracking a remote branch `origin/master`, and remote branches `origin/next`, `origin/pu`, and `origin/maint`. The tracking branches are set up so that if you do something like `git fetch origin`, they'll be fetched as you expect. Any remote branches (in the cloned remote) and other refs are completely ignored.

- `git clone --bare origin-url`: You will get all of the tags copied, local branches `master (HEAD)`, `next`, `pu`, and `maint`, <span style="color:red">no remote tracking branches</span>. That is, all branches are copied as is, and it's set up completely independent, with no expectation of fetching again. Any remote branches (in the cloned remote) and other refs are completely ignored.

- `git clone --mirror origin-url`: Every last one of those refs will be copied as-is. You'll get all the tags, local branches `master (HEAD)`, `next`, `pu`, and `maint`, remote branches `devA/master` and `devB/master`, other refs `refs/foo/bar` and `refs/foo/baz`. Everything is exactly as it was in the cloned remote. Remote tracking is set up so that if you run `git remote update` all refs will be overwritten from origin, as if you'd just deleted the mirror and recloned it. As the docs originally said, it's a mirror. It's supposed to be a functionally identical copy, interchangeable with the original.

总结下来就是:

- `git clone origin-url` 克隆上游仓库的所有标签，本地创建一个 `master (HEAD)` 分支，跟踪到远程分支 `origin/master`，并且设置当前仓库的远程分支为对应的上游仓库的分支.

- `git clone --bare origin-url` 克隆上游仓库的所有标签和本地分支，没有远程跟踪分支，上游仓库本身的所有远程分支和远程引用会被忽略掉.

- `git clone --mirror origin-url` 完整克隆上游仓库，当前仓库的所有标签，分支和引用都与上游仓库完全一致，包括上游仓库的远程分支和远程引用等。使用 `git remote update` 会把上游仓库把的所有分支，标签及引用再次更新到当前仓库，相当于删掉仓库再重新克隆了一次，这就是 **mirror** 所要表达的含义.

### [在 GitHub 上复制仓库](https://help.github.com/en/articles/duplicating-a-repository)

`--bare` 方式

```sh
# bare clone
git clone --bare git@github.com/user/origin-repository.git

cd origin-repository.git

# push all local refs to remote and overwrite them
git push --mirror git@github.com/user/mirror-repository.git
```

`--mirror` 方式

```sh
git clone --mirror git@github.com/user/origin-repository.git

cd origin-repository.git

# 删除镜像中的 refs/pull/* 引用
git show-ref | cut -d' ' -f2 | grep 'pull' | xargs -r -L1 git update-ref -d

git push --mirror git@github.com/user/mirror-repository.git
```

这会将 origin 仓库的所有本地分支，标签以及远程引用全部复制到镜像仓库。`git push --mirror` 会将本地 `refs/` 目录下的所有引用 (包括但不限于 `refs/heads`，`refs/remotes`，`refs/tags`) 全部镜像到远程仓库。

<span style="color:red"><b>注意:</b></span> 不要将 `git push --mirror` 用于非镜像方式克隆的仓库，这种推送会将本地分支和引用覆盖远程仓库中的分支和引用。

## [移除对象](https://git-scm.com/book/zh/v2/Git-内部原理-维护与数据恢复)

```sh
# 找出占用空间最大的数据对象，第三列为文件大小
$ git verify-pack -v .git/objects/pack/pack-a8…30.idx | sort -k 3 -n | tail -3
bf1e07a27a65dfa74d6a500b07c3f5a68c77eceb blob   3279775 2914719 22414147
0aa46bc485eff11ba507eb790e076d5d69ddafa2 blob   8735500 8470799 996362
f63d58997ac0824770af82e255ae39ba2a6eafb4 blob   12036583 11950767 10008956

# 找出数据对象对应的文件名
$ git rev-list --objects --all | grep f63d589
f63d58997ac0824770af82e255ae39ba2a6eafb4 files/default/propel_resmgr_hos.tar.gz

# 找出曾经改动过该文件的提交对象
$ git log --oneline --branches -- 'files/default/propel_resmgr_hos.tar.gz'
9b637b1 - remove the useless files under files/default folder
ee295e7 - changes for hos

# 找出目录对象
$ git log --oneline --branches -- 'fonts/UbuntuMono'
9b637b1 - remove the useless files under files/default folder
fbef434 add favourite fonts

# 从索引或暂存区中删除文件，并重写自 ee295e7 提交以后的历史
$ git filter-branch -f --index-filter 'git rm --ignore-unmatch --cached "files/default/propel_resmgr_hos.tar.gz"' -- ee295e7^..

# 从索引或暂存区中目录及其下的文件，并重写自 fbef434 提交以后的历史
$ git filter-branch -f --index-filter 'git rm -r --ignore-unmatch --cached "fonts/UbuntuMono"' -- fbef434^..

# 删除引用日志和 `.git/refs/original` 中对这个文件引用.
$ rm -rf .git/refs/original
$ rm -rf .git/logs

# 重新打包文件
$ git gc

# 查看空间变化
$ git count-objects -v

# 将产生的"孤立"对象删除
$ git prune --expire now
```

[`git-filter-repo`](https://github.com/newren/git-filter-repo) 是一个 git 历史重写工具，可以方便的从历史中删除文件或目录，更改文件内容，相比于 `filter-branch` 会更加高效。

```sh
# 查看历史提交中含有私钥文件的提交
git log --oneline --all '*.key'

# 删除历史提交中的所有私钥文件
git-filter-repo --invert-paths --path-glob '*.key' --dry-run
```

## [Git 别名](https://git-scm.com/book/zh/v2/Git-基础-Git-别名)

```sh
# 撤消暂存
$ git config --global alias.unstage 'reset HEAD --'
git unstage Readme.md

# 查看最后一次提交
$ git config --global alias.last '-c pager.log= log --decorate --abbrev-commit -1'
git last
git last origin/features

# 格式化输出 log
git config --global alias.logs "log --graph --pretty='%Cred%h%Creset -%C(auto)%d%Creset %s %Cgreen(%ad) %C(bold blue)<%an>%Creset' --date=short"

# 修改已提交历史中的用户名或邮箱
# from
# https://stackoverflow.com/questions/2919878/git-rewrite-previous-commit-usernames-and-emails
# https://github.com/brauliobo/gitconfig/blob/master/configs/.gitconfig
# Usage:
# git change-commits GIT_AUTHOR_NAME "old name" "new name"
# git change-commits GIT_AUTHOR_EMAIL "old@email.com" "new@email.com" HEAD~10..HEAD
change-commits = "!f() { VAR=$1; OLD=$2; NEW=$3; shift 3; git filter-branch --env-filter \"if [[ \\\"$`echo $VAR`\\\" = '$OLD' ]];  then export $VAR='$NEW'; fi\" $@; }; f "

# 删除已提交历史中的某个文件
# 目前推荐使用 `git-filter-repo` 来删除历史文件
# from https://help.github.com/articles/remove-sensitive-data
# Usage:
# git remove-file "abc.tgz"
remove-file = "!f() { git filter-branch -f --index-filter \"git rm --cached --ignore-unmatch $1\" --prune-empty --tag-name-filter  cat -- --all; }; f"
```

首先使用格式化输出 log 找出历史提交中所有的用户名和邮箱名

```sh
# 找出历史提交中使用过的用户名
git log --format=%an | sort -u

# 找出历史提交中使用过的邮箱名
git log --format=%ae | sort -u

# 修改历史提交中的用户名
git change-commits GIT_AUTHOR_NAME 'old name' 'new name'

# 修改历史提交中的邮箱名
git change-commits GIT_AUTHOR_EMAIL 'old@email.com' 'new@email.com'
```

## 其它有用的命令

### 在历史提交中查找文件（包括已删除文件）

```sh
git log --oneline --all -- 'file-full-path'

git log --oneline --all --full-history -- 'file-full-path'
```

如果只想查看该文件的最后一次改动提交，可以加上 `-1` 参数

```sh
git log --oneline --all -1 -- 'file-full-path'

git log --oneline --all --full-history -1 -- 'file-full-path'
```

### 在历史提交中查找文件内容的改动

例如，查找字符串 `-----BEGIN RSA PRIVATE KEY-----` 何时被添加，何时被改动，何时被删除

```sh
git log --oneline --all -S '-----BEGIN RSA PRIVATE KEY-----'
```

如果需要更复杂的字符串查找，可以使用正则表达式选项 `-G<regex>`。

## 使用 SSH KEY 访问 Github

使用`ssh-keygen`将生成的公钥添加到 Github 账户，在本地通过`git@github.com:username/repo.git`访问。
`git`命令默认使用`~/.ssh/id_rsa`，如果需要使用其它私钥，需要将私钥添加到`ssh-agent`

```sh
# 启动ssh agent
eval $(ssh-agent -s)

# 把认证用的私钥添加到ssh agent
ssh-add ~/.ssh/private_key

# 查看ssh agent中的所有身份信息
ssh-add -l

# 查看ssh agent中的所有身份的公钥信息
ssh-add -L

# 删除ssh agent中的身份信息(不指定密钥文件的话默认使用~/.ssh/id_rsa)
ssh-add -d ~/.ssh/private_key

# 删除ssh agent中的所有身份信息
ssh-add -D

# 测试Git访问
ssh -T git@github.com
```

## 有用链接

[git - 简明指南](http://rogerdudler.github.io/git-guide/index.zh.html)  
[一张图看明白 Git 的四个区五种状态](http://imtuzi.com/post/git-four-areas-five-states.html)  
[99%的时间在使用的 Git 命令](http://imtuzi.com/post/most-used-git-cmd.html)