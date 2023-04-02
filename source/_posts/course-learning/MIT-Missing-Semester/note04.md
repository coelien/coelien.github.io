---
title: MIT课程笔记： Notes on Missing semester 04
tags: 
- 命令行环境
- MIT课程
categories:
- 课程学习
- 命令行环境
---

# Command-line Environment

## Job Control

- lots of SIGNALS to control jobs

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202211180939370.png" alt="image-20221118093932091" style="zoom:33%;" />

- The flowing commands show how to control jobs in terminal. **KILL** can be useful because it can send signals to the programs. `bg` can continue processes running on the background.

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202211180932343.png" alt="image-20221118093238127" style="zoom: 33%;" />

- why should we use `nohup` when closing the terminal? 

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202211180934820.png" alt="image-20221118093425765" style="zoom:33%;" />

- Because by default the hangup will terminate the job. So what `nohup` do is pretty simple, it actually ignore the hangup signal. But the KILL signal can't be ignored no matter what.

## Terminal Multiplexers

### CORE Concept in Tmux

**structure**

Sessions

- Windows
  - Panes

**what happens when we type tmux?**

- In our shell, we started a process called tmux, and tmux started a different process which is the shell we are currently in. 

**The key bindings the core to use tmux efficiently**

- form: prefix＋key

**how to split the current display into panes?**

- `ctrl B + "`: split horizontally
- `ctrl B + %`: split vertically
- `ctrl B + arrow keys`: navigate through different panes

**So if you find panes are too small, how to deal with it?**

- `ctrl B + z`: zoom out & zoom in

### WHAT ACTUALLY DOES TMUX DO ?

- Give you a more convenient visual layout, that you can quickly manipulate through.
- if we start a tmux session, it won't be affected by the ssh connections（hangup signal）.

## Dot Files

### what does Dot files do?

- how to configure your shell to do the things you want to do.
- mainly about how to do them quicker and in a more convenient way.

## Remote Machines

