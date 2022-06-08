---
title: Bash Reference Manual
date: 2016-06-20
modify_date: 2020-09-11
tags: Bash
key: Bash-Reference-Manual-2016-06-20
---

[Bash参考手册][Bash-Reference-Manual]

Bash 常用语法及相关概念

<!--more-->

## [3.2 Shell 命令][Shell-Commands]

### [3.2.4 复合命令][Compound-Commands]

#### [3.2.4.1 循环结构][Looping-Constructs]

##### until

```bash
until test-commands; do commands; done
```

##### while

```bash
while test-commands; do commands; done
```

##### for

```bash
for var in words; do pass ${var} to commands; done
```

#### [3.2.4.2 条件结构][Conditional-Constructs]

##### if

```bash
if test-commands; then
    commands;
elif test-commands; then
    commands;
else
    commands;
if
```

##### case

##### select

##### (())

```bash
((arithmetic expression))
```

对算术表达式求值，如果计算结果非零，则该测试返回状态为 `0`，否则返回状态为 `1`。

##### [[]]

```bash
[[conditional expression]]
```

对条件表达式求值，根据结果返回状态为 `0` 或 `1`。

#### [3.2.4.3 命令组合][Command-Grouping]

Bash 提供了两种方式来把一系列命令放在一起作为整体执行。命令组合在一起，可以对整个命令列表进行重定向。例如，命令列表中所有命令的输出都可以重定向到单一流中。

##### ()

```bash
( list )
```

把一系列命令放在 `()` 中会创建一个subshell环境，并在subshell中执行每条命令。subshell结束后，该环境中的变量赋值不再有效。

##### {}

```bash
{ list; }
```

把一系列命令放在 `{}` 中会在当前shell中执行每条命令，不会创建subshell。命令列表最后的 `;` 是必须的。

## [3.5 Shell 扩展][Shell-Expansions]

### [3.5.4 命令替换][Command-Substitution]

命令替换用命令的输出取代命令本身。

```bash
$(command)
```

或

```bash
`command`
```

### [3.5.5 算术扩展][Arithmetic-Expansion]

算术扩展可以对算术表达式求值并替换成的求值的结果。

```bash
$((arithmetic expression))
```

## [3.7 执行命令][Executing-Commands]

### [3.7.5 退出状态][Exit-Status]

已执行命令的退出状态是 `waitpid` 系统调用或其等效函数的返回值。退出状态介于 `0` 到 `255` 之间，但是，如下所述，Shell可能会特别使用 `125` 以上的值。Shell的内置命令和复合命令的退出状态也限制在此范围内。在某些情况下，Shell将使用特殊值来指示具体的失败状态。

以Shell的角度看，<span style="color:red"><b>命令执行成功，退出为 `0`。`非0` 退出表示失败。</b></span>之所以使用这种不直观的方式，是因为可以很好的定义成功和各种失败状态。当命令以关键信号 $N$ 结束，Bash使用 `128+N` 的值作为退出状态。

如果找不到命令，执行该命令的子进程将返回状态 `127`。如果找到但不能执行该命令，则返回状态为 `126` 。

如果命令由于扩展或重定向期间的错误而失败，则退出状态 `大于0`。

退出状态用于 Bash 的条件命令(`if`, `case`, `select`, `(())`, `[[]]`)和部分命令序列(`cmd1 && cmd2`, `cmd1 || cmd2`)。

Bash 的所有内部命令都会在成功时返回 `0`，失败时返回 `非0`，因此可用于条件命令和命令序列中。所有内建函数都返回退出状态 `2` ，表示命令使用不正确，通常指选项无效或缺少参数。

## [6.4 Bash 条件表达式][Bash-Conditional-Expressions]

条件表达式由复合命令 `[[` 以及内部命令 `test` 和 `[` 使用。

简短总结一下就是:

- `test` 实现了老式的，可移植的命令语法。在几乎所有的 shell 中 (除了最古老的 Bourne shell)，`[` 就是 `test` 的同义词，只是 `[` 需要参数 `]` 表示结尾。尽管所有现代 shell 都有 `[` 命令的内置实现，但系统依然有一个该名称的外部命令 `/bin/[`。POSIX 为 `[` 定义了必需的功能集，但几乎所有 shell 都对其提供了扩展。因此，如果你想要可移植的代码，应当注意不要使用这些扩展。

- `[[` 是对 `test` (或者 `[`) 的增强，但它是 shell 语法中的**关键字**而不是一个命令。因其语法特性，你可以在其中使用 `&&` 和 `||` 进行布尔测试或者使用 `>` 和 `<` 进行字符串比较。`[` 无法做到这些是因为它是个普通命令，而 `&&`, `||`, `<`, `>` 不能作为命令行参数传递给它。如果编写 `sh` 兼容的脚本，那么仍然需要使用 `[`，如果使用了 `[[`，要确保脚本开头有 `#!/bin/bash` shebang 行。

| Feature                    | new test `[[` | old test `[`      | Example                                                          |
| -------------------------- | ------------- | ----------------- | ---------------------------------------------------------------- |
| string comparison          | `>`           | `\> (*)`          | `[[ a > b ]] || echo "a does not come after b"`                  |
|                            | `<`           | `\< (*)`          | `[[ az < za ]] && echo "az comes before za"`                     |
|                            | `= (or ==)`   | `=`               | `[[ a = a ]] && echo "a equals a"`                               |
|                            | `!=`          | `!=`              | `[[ a != b ]] && echo "a is not equal to b"`                     |
| integer comparison         | `-gt`         | `-gt`             | `[[ 5 -gt 10 ]] || echo "5 is not bigger than 10"`               |
|                            | `-lt`         | `-lt`             | `[[ 8 -lt 9 ]] && echo "8 is less than 9"`                       |
|                            | `-ge`         | `-ge`             | `[[ 3 -ge 3 ]] && echo "3 is greater than or equal to 3"`        |
|                            | `-le`         | `-le`             | `[[ 3 -le 8 ]] && echo "3 is less than or equal to 8"`           |
|                            | `-eq`         | `-eq`             | `[[ 5 -eq 05 ]] && echo "5 equals 05"`                           |
|                            | `-ne`         | `-ne`             | `[[ 6 -ne 20 ]] && echo "6 is not equal to 20"`                  |
| conditional evaluation     | `&&`          | `-a (**)`         | `[[ -n $var && -f $var ]] && echo "$var is a file"`              |
|                            | `\|\|`        | `-o (**)`         | `[[ -b $var || -c $var ]] && echo "$var is a device"`            |
| expression grouping        | `(...)`       | `\( ... \)` (**)  | `[[ $var = img* && ($var = *.png || $var = *.jpg) ]] && "echo "$var starts with img and ends with .jpg or .png"` |
| Pattern matching           | `= (or ==)`   | (not available)   | `[[ $name = a* ]] || echo "name does not start with an 'a': $name"`|
| RegularExpression matching | `=~`          | (not available)   | `[[ $(date) =~ ^Fri\ ...\ 13 ]] && echo "It's Friday the 13th!"` |

(*) This is an extension to the POSIX standard; some shells may have it, others may not.

(**) The `-a` and `-o` operators, and `( ... )` grouping, are defined by POSIX but only for strictly limited cases, and are marked as deprecated. Use of these operators is discouraged; you should use multiple `[` commands instead:

- `if [ "$a" = a ] && [ "$b" = b ]; then ...`
- `if [ "$a" = a ] || { [ "$b" = b ] && [ "$c" = c ];}; then ...`

各类文件测试操作符: `-a`, `-b`, `-d`, `-e`, `-f`, ...

更多差异，可以参考以下链接:

- [Bash FAQ - What is the difference between test, \[ and \[\[ ?](https://mywiki.wooledge.org/BashFAQ/031)
- [Bash Practices - Bash Tests](https://mywiki.wooledge.org/BashGuide/Practices#Bash_Tests)
- [Stack Overflow - What's the difference between \[in Bash?](https://stackoverflow.com/questions/3427872/whats-the-difference-between-and-in-bash)

[Bash-Reference-Manual]: https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html
[Shell-Commands]: https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html#Shell-Commands
[Compound-Commands]: https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html#Compound-Commands
[Looping-Constructs]: https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html#Looping-Constructs
[Conditional-Constructs]: https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html#Conditional-Constructs
[Command-Grouping]: https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html#Command-Grouping
[Shell-Expansions]: https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html#Shell-Expansions
[Command-Substitution]: https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html#Command-Substitution
[Arithmetic-Expansion]: https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html#Arithmetic-Expansion
[Executing-Commands]: https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html#Executing-Commands
[Exit-Status]: https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html#Exit-Status
[Bash-Conditional-Expressions]: https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html#Bash-Conditional-Expressions
