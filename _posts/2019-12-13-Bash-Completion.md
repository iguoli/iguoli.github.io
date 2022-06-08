---
title: Bash Completion
date: 2019-12-04
modify_date: 2020-09-11
tags: Bash
key: Bash-Completion-2019-12-13
---

为特定命令编写 Bash 下的自动补全函数。

<!--more-->

## SSH 服务器名称自动补全

```sh
$ mkdir -p /etc/bash_completion.d

# ssh 的自动补全代码 
$ vim /etc/bash_completion.d/ssh
_ssh()
{
    local cur prev opts
    COMPREPLY=()
    cur="${COMP_WORDS[COMP_CWORD]}"
    prev="${COMP_WORDS[COMP_CWORD-1]}"
    opts=$(grep '^Host' ~/.ssh/config ~/.ssh/config.d/* 2>/dev/null | grep -v '[?*]' | cut -d ' ' -f 2-)

    COMPREPLY=( $(compgen -W "$opts" -- ${cur}) )
    return 0
}
complete -F _ssh ssh

$ echo "source /etc/bash_completion.d/ssh" >> ~/.bashrc

$ source ~/.bashrc
```

## 参考链接

- [Autocomplete server names for SSH and SCP](https://unix.stackexchange.com/questions/136351/autocomplete-server-names-for-ssh-and-scp)
- [An introduction to bash completion: part 1](https://debian-administration.org/article/316/An_introduction_to_bash_completion_part_1)
- [An introduction to bash completion: part 2](https://debian-administration.org/article/317/An_introduction_to_bash_completion_part_2)
- [Bash Variables](https://www.gnu.org/software/bash/manual/html_node/Bash-Variables.html#Bash-Variables)
