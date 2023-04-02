---
title: MIT课程笔记： Notes on Missing semester 03
tags: 
- 数据处理
- MIT课程
categories:
- 课程学习
- Data Wrangling
---

# Data Wrangling

## Conception
- Data Wrangling: Anything that goes from one piece of data to another representation of data.

- massage data until you end up with exactly what you wanted

## Preparation to Data Wrangling

- data to wrangle
- useful tools to do with data

## Useful Tools

**Regular Expressions**

Common patterns for regular expressions:

- `.` : any single character
- `*` : zero or more of the preceding match
- `+` : one or more of the preceding match
- `[abc]`: any one character of a,b and c
- `(RX1|RX2)` : either something that matches RX1 or RX2
- `^` : the start of the line
- `$` : the end of the line

something need to **be kept in mind** : `+` and `*` are default greedy, it means that it will find the max match. In some implementations, we can suffix `*` or`+` with a `?` to make them non-greedy. But sed doesn't support it. So we could switch to perl's command-line mode to do this:

```perl
perl -pe 's/.*?Disconnected from//'
```

**sed**

- a stream editor: you give short commands for how to modify the file.  The most common command is `s`: substitution.

- s command: `s/REGEX/SUBSTITUTION`, REGEX is the regular expression you want to search for.

- note: we should pass `-E` to give them their special meaning

- get username out of the log file:

  use what called capture group

```shell
cat ssh.log
| grep sshd
| grep "Disconnected from"
| sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
```

- sed can do a lot of interesting things: injecting text, printing lines and selecting lines by index( check `man sed` for more info)

**sort**

- can be useful when the content has numerical attributes
- for example, find the most common usernames, -n means sorting in numeric order.

```shell
cat ssh.log
| grep sshd
| grep "Disconnected from"
| sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
| sort | uniq -c
| sort -nk1,1 | tail -n10
```

**awk & paste**

- if we want to extract only the usernames as a comma-separated list:

```shell
cat ssh.log
| grep sshd
| grep "Disconnected from"
| sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
| sort | uniq -c
| sort -nk1,1 | tail -n10
| awk '{print $2}' | paste -sd,
```

- `paste -sd,` means  combine lines (-s) by delimiter(-d) `,`

**awk**

> further link to learn more: [here](https://backreference.org/2010/02/10/idiomatic-awk/)

awk is a nice programming language that is good at processing text streams. 

- if we want to compute the number of single-use username that start with c and end with e:

```shell
| awk '$1 == 1 && $2 ~ /^c[^ ]*e$/ {print $2}' | wc -l
```

- of course we can use another way to do the same thing:

```shell
awk 'BEGIN {rows=0} $1 == 1 && $2 ~ /^c[^ ]*e$/ {rows += $1} END {print rows}'
```

## Data Analysis

**bc**

a calculator that can read from stdin

**R**

专注于数据分析和画图的语言（it is not that difficult. Try to have fun!）

**gnuplot**

可以制作复杂的图表. demo: https://gnuplot.sourceforge.net/demo_5.4/

