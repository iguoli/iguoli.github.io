---
title: 正则表达式比较(Regex - BREs, EREs, PCRE)
date: 2019-12-25
modify_date: 2023-06-14
tags: Regex
key: Regex-Cheatsheet-2019-12-25
---

常见的几个正则库的语法比较。 原链接: [Regex cheatsheet](https://remram44.github.io/regex-cheatsheet/regex.html)

## Regex cheatsheet

Many programs use regular expression to find & replace text. However, they tend to come with their own different flavor.

You can probably expect most modern software and programming  languages to be using some variation of the Perl flavor, "PCRE"; however command-line tools (grep, less, ...) will often use the POSIX flavor  (sometimes with an extended variant, e.g. `egrep` or `sed -r`). ViM also comes with its own syntax (a superset of what Vi accepts).

This cheatsheet lists the respective [syntax of each flavor](#syntax), and the [software that uses it](#programs).

_Extended Regular Expression_ 有时可以通过命令行标志 `-E` 与 Unix 实用程序一起使用。其他 Unix 实用程序，如 `awk`，默认使用 [ERE]。

[ERE] 与 [BRE][ERE] 的主要区别在于删除了一些反斜杠：`\{...\}` 变为 `{...}` 并且 `\(...\)` 变为 `(...)`。

<!--more-->

## Syntax

| What                                                | [Perl][PCRE]/PCRE                                | [Python's `re`][PythonRE]             | POSIX ([BRE][ERE])                 | POSIX extended ([ERE])         | ViM                                      |
| --------------------------------------------------- | ------------------------------------------------ | ------------------------------------- | ---------------------------------- | ------------------------------ | ---------------------------------------- |
| ___Basics___                                        |                                                  |                                       |                                    |                                |                                          |
| Custom character class                              | `[...]`                                          | `[...]`                               | `[...]`                            | `[...]`                        | `[...]`                                  |
| Negated custom character class                      | `[^...]`                                         | `[^...]`                              | `[^...]`                           | `[^...]`                       | `[^...]`                                 |
| \ special in class?                                 | yes                                              | yes                                   | no, `]` escaped if comes first     | no, `]` escaped if comes first | yes                                      |
| Ranges                                              | `[a-z]`, `-` escaped if comes last               | `[a-z]`, `-` escaped if first or last | `[a-z]`, `-` escaped if comes last |                                | `[a-z]`, `-` escaped if comes last       |
| Alternation    | `|` | `|` | `\|` | `|` | `\|` `\&` (low precedence) |
| Escaped character                                   | `\033` `\x1B` `\x{1234}` `\N{name}` `\N{U+263D}` | `\x12`                                |                                    |                                | `\%d123` `\%x2A` `\%u1234` `\%U1234ABCD` |
| ___Character classes___                                   |                                                  |                                       |                                    |                                |                                          |
| Any character (except newline)                      | `.`                                              | `.`                                   | `.`                                | `.`                            | `.`                                      |
| Any character (including newline)                   |                                                  |                                       |                                    |                                | `\_.`                                    |
| Match a "word" character (alphanumeric plus `_`)    | `\w` `[[:word:]]`                                | `\w`                                  | `\w`                               | `\w`                           | `\w`                                     |
| Case                                                | `[[:upper:]]` / `[[:lower:]]`                    |                                       | `[[:upper:]]` / `[[:lower:]]`      | `[[:upper:]]` / `[[:lower:]]`  | `\u` `[[:upper:]]` / `\l` `[[:lower:]]`  |
| Match a non-"word" character                        | `\W`                                             | `\W`                                  |                                    |                                | `\W`                                     |
| Match a whitespace character (except newline)       |                                                  |                                       | `\s` `[[:space:]]`                 | `\s` `[[:space:]]`             | `\s` `[[:space:]]`                       |
| Whitespace including newline                        | `\s` `[[:space:]]`                               | `\s`                                  |                                    |                                | `\_s`                                    |
| Match a non-whitespace character                    | `\S`                                             | `\S`                                  | `[^[:space:]]`                     | `[^[:space:]]`                 | `\S` `[^[:space:]]`                      |
| Match a digit character                             | `\d` `[[:digit:]]`                               | `\d`                                  | `[[:digit:]]`                      | `[[:digit:]]`                  | `\d` `[[:digit:]]`                       |
| Match a non-digit character                         | `\D`                                             | `\D`                                  | `[^[:digit:]]`                     | `[^[:digit:]]`                 | `\D` `[^[:digit:]]`                      |
| Any hexadecimal digit                               | `[[:xdigit:]]`                                   |                                       | `[[:xdigit:]]`                     | `[[:xdigit:]]`                 | `\x` `[[:xdigit:]]`                      |
| Any octal digit                                     |                                                  |                                       |                                    |                                | `\o`                                     |
| Any graphical character excluding "word" characters | `[[:punct:]]`                                    |                                       | `[[:punct:]]`                      | `[[:punct:]]`                  | `[[:punct:]]`                            |
| Any alphabetical character                          | `[[:alpha:]]`                                    |                                       | `[[:alpha:]]`                      | `[[:alpha:]]`                  | `\a` `[[:alpha:]]`                       |
| Non-alphabetical character                          |                                                  |                                       | `[^[:alpha:]]`                     | `[^[:alpha:]]`                 | `\A` `[^[:alpha:]]`                      |
| Any alphanumerical character                        | `[[:alnum:]]`                                    |                                       | `[[:alnum:]]`                      | `[[:alnum:]]`                  | `[[:alnum:]]`                            |
| ASCII                                               | `[[:ascii:]]`                                    |                                       |                                    |                                |                                          |
| Character equivalents (e = é = è) (as per locale)   |                                                  |                                       | `[[=e=]]`                          | `[[=e=]]`                      | `[[=e=]]`                                |
| ___Zero-width assertions___                         |                                                  |                                       |                                    |                                |                                          |
| Word boundary                                       | `\b`                                             | `\b`                                  | `\b`                               | `\b`                           | `\<`  `\>`                               |
| Anywhere but word boundary                          | `\B`                                             | `\B`                                  | `\B`                               | `\B`                           |                                          |
| Beginning of line/string                            | `^` / `\A`                                       | `^` / `\A`                            | `^`                                | `^`                            | `^` (beginning of pattern ) `\_^`        |
| End of line/string                                  | `$` / `\Z`                                       | `$` / `\Z`                            | `$`                                | `$`                            | `$` (end of pattern) `\_$`               |
| ___Captures and groups___                           |                                                  |                                       |                                    |                                |                                          |
| Capturing group                                     | `(...)` `(?...)`                                 | `(...)` `(?P...)`                     | `\(...\)`                          | `(...)`                        | `\(...\)`                                |
| Non-capturing group                                 | `(?:...)`                                        | `(?:...)`                             |                                    |                                | `\%(...\)`                               |
| Backreference to a specific group.                  | `\1` `\g1` `\g{-1}`                              | `\1`                                  | `\1`                               | `\1` non-official              | `\1`                                     |
| Named backreference                                 | `\g{name}` `\k`                                  | `(?P=name)`                           |                                    |                                |                                          |
| ___Look-around___                                         |                                                  |                                       |                                    |                                |                                          |
| Positive look-ahead                                 | `(?=...)`                                        | `(?=...)`                             |                                    |                                | `\(...\)\@=`                             |
| Negative look-ahead                                 | `(?!...)`                                        | `(?!...)`                             |                                    |                                | `\(...\)\@!`                             |
| Positive look-behind                                | `(?<=...)`                                       | `(?<=...)`                            |                                    |                                | `\(...\)\@<=`                            |
| Negative look-behind                                | `(?<!...)`                                       | `(?<!...)`                            |                                    |                                | `\(...\)\@<!`                            |
| ___Multiplicity___                                  |                                                  |                                       |                                    |                                |                                          |
| 0 or 1                                              | `?`                                              | `?`                                   | `\?`                               | `?`                            | `\?`                                     |
| 0 or more                                           | `*`                                              | `*`                                   | `*`                                | `*`                            | `*`                                      |
| 1 or more                                           | `+`                                              | `+`                                   | `\+`                               | `+`                            | `\+`                                     |
| Specific number                                     | `{n}` `{n,m}` `{n,}`                             | `{n}` `{n,m}` `{n,}`                  | `\{n\}` `\{n,m\}` `\{n,\}`         | `{n}` `{n,m}` `{n,}`           | `\{n}` `\{n,m}` `\{n,}`                  |
| 0 or 1, non-greedy                                  | `??`                                             | `??`                                  |                                    |                                |                                          |
| 0 or more, non-greedy                               | `*?`                                             | `*?`                                  |                                    |                                | `\{-}`                                   |
| 1 or more, non-greedy                               | `+?`                                             | `+?`                                  |                                    |                                |                                          |
| Specific number, non-greedy                         | `{n,m}?` `{n,}?`                                 | `{n,m}?` `{n,}?`                      |                                    |                                | `\{-n,m}` `\{-n,}`                       |
| 0 or 1, don't give back on backtrack                | `?+`                                             |                                       |                                    |                                |                                          |
| 0 or more, don't give back on backtrack             | `*+`                                             |                                       |                                    |                                |                                          |
| 1 or more, don't give back on backtrack             | `++`                                             |                                       |                                    |                                |                                          |
| Specific number, don't give back on backtrack       | `{n,m}+` `{n,}+`                                 |                                       |                                    |                                |                                          |
| ___Other___                                         |                                                  |                                       |                                    |                                |                                          |
| Independent non-backtracking pattern                | `(?>...)`                                        |                                       |                                    |                                | `\(...\)\@>`                             |
| Make case-sensitive/insensitive                     | `(?i)` / `(?-i)`                                 | `(?i)` / `(?-i)`                      |                                    |                                | `\c` / `\C`                              |

## Programs

| What                                   | Syntax                                              | Comments/gotchas                                                                  |
| -------------------------------------- | --------------------------------------------------- | --------------------------------------------------------------------------------- |
| ___Programming languages___            |                                                     |                                                                                   |
| [Perl]                                 | PCRE                                                | PCRE is actually a separate implementation from Perl's, with [slight differences] |
| [Python's `re` standard lib][PythonRE] | Python's own syntax (Perl-inspired)                 |                                                                                   |
| [Ruby]                                 | Ruby's own syntax (Perl-inspired)                   |                                                                                   |
| [Java's java.util.regex][Java]         | Almost PCRE                                         |                                                                                   |
| [Boost.Regex]                          | PCRE                                                |                                                                                   |
| ___Text editors___                     |                                                     |                                                                                   |
| [Eclipse]                              | PCRE                                                |                                                                                   |
| Emacs                                  | ?                                                   |                                                                                   |
| Netbeans                               | PCRE                                                |                                                                                   |
| Notepad++                              | PCRE (Boost.Regex)                                  |                                                                                   |
| PyCharm                                | PCRE                                                | Perl-inspired                                                                     |
| Sublime Text                           | ?                                                   |                                                                                   |
| UltraEdit                              | PCRE                                                |                                                                                   |
| ViM                                    | ViM                                                 |                                                                                   |
| ___Command-line tools___               |                                                     |                                                                                   |
| awk                                    | ERE                                                 | might depend on the implementation                                                |
| grep                                   | BRE, `egrep` for ERE, `grep -P` for PCRE (optional) |                                                                                   |
| less                                   | ERE                                                 | usually; man page says "regular expression library supplied by your system"       |
| screen                                 | plain text                                          |                                                                                   |
| sed                                    | BRE, `-E` switches to ERE                           |                                                                                   |

[PCRE]: http://perldoc.perl.org/perlre.html
[PythonRE]: https://docs.python.org/library/re.html
[ERE]: https://en.wikibooks.org/wiki/Regular_Expressions/POSIX-Extended_Regular_Expressions

[perl]: http://perldoc.perl.org/perlre.html
[slight differences]: http://en.wikipedia.org/wiki/Perl_Compatible_Regular_Expressions#Differences_from_Perl
[Ruby]: http://ruby-doc.org/core-2.2.0/Regexp.html
[Java]: http://docs.oracle.com/javase/8/docs/api/java/util/regex/Pattern.html
[Boost.Regex]: http://www.boost.org/doc/libs/1_49_0/libs/regex/doc/html/boost_regex/syntax/perl_syntax.html
[Eclipse]: http://www.eclipse.org/tptp/home/downloads/installguide/gla_42/ref/rregexp.html