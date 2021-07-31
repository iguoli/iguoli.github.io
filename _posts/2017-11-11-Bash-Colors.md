---
title: Bash Colors
date: 2017-11-11
modify_date: 2017-11-11
tags: Bash
key: Bash-Colors-2017-11-11
---

Bash 中使用 `echo` 输出颜色代码的格式

| 代码                 | 说明                            |
| -------------------- | ------------------------------- |
| `\003[` or `\e`      | 颜色起始修饰符                  |
| ***STYLE*;*COLOR*m** | 样式 + **;** + 颜色代码 + **m** |
| `\003[0m` or `\e[0m` | 重置为无颜色                    |

例如：

```bash
echo -e "\e[0;31mText\e[0m"

# Color Variables
NC='\003[0m'
Red='\003[0;31m'

echo -e "${Red}Text${NC}"
```

<!--more-->

## Bash, Hex, Octal 三种形式的转义字符

| modifier | bash  | hex    | octal   | NOTE                         |
| -------- | ----- | ------ | ------- | ---------------------------- |
| start    | \e    | \x1b   | \033    |                              |
| start    | \E    | \x1B   | -       | x cannot be capital          |
| end      | \e[0m | \x1m0m | \033[0m |                              |
| end      | \e[m  | \x1b[m | \033[m  | 0 is appended if you omit it |

## Style

只修改颜色，不影响字符

| Style    | Code |
| -------- | ---- |
| Normal   | 0    |
| **Bold** | 1;   |
| Light    | 2;   |

只修改字符的样式，不影响颜色。

| Style            | Code |
| ---------------- | ---- |
| <I>Italic</I>    | 3;   |
| <U>Underline</U> | 4;   |
| Blinking (slow)  | 5;   |
| Blinking (fast)  | 6;   |
| Reverse          | 7;   |
| Hide             | 8;   |
| Cross-out        | 9;   |


## Color

| Color      | Foreground Code | Background Code |
| ---------- | --------------- | --------------- |
| Black      | 30              | 40              |
| Red        | 31              | 41              |
| Green      | 32              | 42              |
| Brown      | 33              | 43              |
| Blue       | 34              | 44              |
| Purple     | 35              | 45              |
| Cyan       | 36              | 46              |
| Light Gray | 37              | 47              |

## Color Variables

```bash
# No Color
NC='\033[0m'              # Text Reset

# Regular Colors
Black='\033[0;30m'        # Black
Red='\033[0;31m'          # Red
Green='\033[0;32m'        # Green
Yellow='\033[0;33m'       # Yellow
Blue='\033[0;34m'         # Blue
Purple='\033[0;35m'       # Purple
Cyan='\033[0;36m'         # Cyan
White='\033[0;37m'        # White

# Bold
BBlack='\033[1;30m'       # Black
BRed='\033[1;31m'         # Red
BGreen='\033[1;32m'       # Green
BYellow='\033[1;33m'      # Yellow
BBlue='\033[1;34m'        # Blue
BPurple='\033[1;35m'      # Purple
BCyan='\033[1;36m'        # Cyan
BWhite='\033[1;37m'       # White

# Light
LBlack='\033[2;30m'       # Black
LRed='\033[2;31m'         # Red
LGreen='\033[2;32m'       # Green
LYellow='\033[2;33m'      # Yellow
LBlue='\033[2;34m'        # Blue
LPurple='\033[2;35m'      # Purple
LCyan='\033[2;36m'        # Cyan
LWhite='\033[2;37m'       # White

# Italic
IBlack='\033[3;30m'       # Black
IRed='\033[3;31m'         # Red
IGreen='\033[3;32m'       # Green
IYellow='\033[3;33m'      # Yellow
IBlue='\033[3;34m'        # Blue
IPurple='\033[3;35m'      # Purple
ICyan='\033[3;36m'        # Cyan
IWhite='\033[3;37m'       # White

# Underline
UBlack='\033[4;30m'       # Black
URed='\033[4;31m'         # Red
UGreen='\033[4;32m'       # Green
UYellow='\033[4;33m'      # Yellow
UBlue='\033[4;34m'        # Blue
UPurple='\033[4;35m'      # Purple
UCyan='\033[4;36m'        # Cyan
UWhite='\033[4;37m'       # White

# Blinking
KBlack='\033[5;30m'       # Black
KRed='\033[5;31m'         # Red
KGreen='\033[5;32m'       # Green
KYellow='\033[5;33m'      # Yellow
KBlue='\033[5;34m'        # Blue
KPurple='\033[5;35m'      # Purple
KCyan='\033[5;36m'        # Cyan
KWhite='\033[5;37m'       # White

# Reverse
RBlack='\033[7;30m'       # Black
RRed='\033[7;31m'         # Red
RGreen='\033[7;32m'       # Green
RYellow='\033[7;33m'      # Yellow
RBlue='\033[7;34m'        # Blue
RPurple='\033[7;35m'      # Purple
RCyan='\033[7;36m'        # Cyan
RWhite='\033[7;37m'       # White

# Background
On_Black='\033[40m'       # Black
On_Red='\033[41m'         # Red
On_Green='\033[42m'       # Green
On_Yellow='\033[43m'      # Yellow
On_Blue='\033[44m'        # Blue
On_Purple='\033[45m'      # Purple
On_Cyan='\033[46m'        # Cyan
On_White='\033[47m'       # White

# Usage
echo -e "${Red}Error${NC} Text"
echo -e "${IGreen}Success${NC}"
```

## 参考链接

[How to change the output color of echo in Linux](https://stackoverflow.com/questions/5947742/how-to-change-the-output-color-of-echo-in-linux)