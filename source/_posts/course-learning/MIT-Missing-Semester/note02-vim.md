---
title: MIT课程笔记： Notes on Missing semester 02
tags: 
- vim
- MIT课程
categories:
- 课程学习
- vim
---

# vim

## 一个有趣的idea

> programming vim through its interface

- vim接口(interfaces)自身就是一个编程语言
- 我们可以组合多种按键以达到难以置信的effect

- 一旦形成肌肉记忆，我们就能以思考的速度来编辑文件！

## vim模式

- normal
- insert
- replace
- visual
  - plain
  - line
  - block
- command-line

## vim基础

**navigation**

- `w` move the cursor foward by one word

- `b` move the cursor backward by one word
- `e` move to the end of the word
- `0` move to the begin of the line
- `$` move to the end of the line
- `^` move to the first non-empty word of the line
- `ctrl-D`  scroll down
- `ctrl-U` scroll up
- `G` move to the end of the buffer
- `gg` move to the begin of the buffer
- `L` move the cursor to the lowest line shown on the screen
- `M` move the cursor to the middle line shown on the screen
- `H` move the cursor to the highest line shown on the screen

- `f+charactor` find(and move the cursor on top of it) the first charactor of the line after the cursor
- `F+charactor` find (and move the cursor on top of it) the first charactor of the line before the cursor
- `t/T` works same but stop until it finds the charactor
- `%` jump between pairs like ([],{}, ...)

**edition**

- `o` open a new line below the cursor
- `O` open a new line above the cursor
- `d + movement key` only delete
- `c + movement key` delete and change

**select, copy & paste**

- `v & motion key` select
- `V & motion key` select lines
- `ctrl-V & motion key` select rectangle region of text

- `y + motion key` copy

**counts**

- `digit + command` 表示重复做几次某条命令 

**modifier**

- `d & i & [` delete things in []
- `c & i & [` delete things in [] and change

- `d & a & [` delete things in [] (include [])



## vim控制命令

**:help + key(命令)**

查看key或命令的用途(帮助文档)

**:qa**

退出全部打开的文件

## Buffers、Tabs、Windows

buffers又叫打开的文件，在vim中打开的文件与windows没有一对一的对应关系。这样有好处，可以同时看同一个文件的两个不同位置的内容

**window**

- 打开一个Window `:sp`

**tab**

- 打开一个Tab `:tabnew`
- 在tab间切换 `gt`