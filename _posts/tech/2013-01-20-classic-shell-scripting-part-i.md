---
layout: post
title: "从零开始，掌握SHELL脚本 -- PART I"
description: ""
category: tech
tags: [shell]
---
{% include JB/setup %}

近期把工作环境完全迁移到MAC上了。我决定好好补习下SHELL脚本的相关知识，运用到日常工作中以达到提高效率的目的。偶得《Classic.Shell.Scripting》一书，故静下心来研读之。本系列文章便是我在通读该书之后的总结。

## UNIX哲学

UNIX的软件哲学经过了时间的历练与考验，其价值得到了充分体现。让我们先温故后知新：

* Do one thing well

  一个程序最好只做一件事，并把这件事做好。

* Process lines of text, not binary

  处理文本数据，而不是二进制数据。

* Use regular expressions

  使用正则表达式

* Default to standard I/O

  默认从standard input获取数据，处理完毕后，向standard output输出数据。

* Don't be chatty

  不要给多余的提示信息，或者确认信息。完全信任用户知道自己在做什么。

* Generate the same output format accepted as input

  输入、输出的数据格式应当一致，这样该程序比较容易在PIPE中使用。

* Let someone else do the hard part

  不要重复造轮子，开始干活之前，先捡起手边已有的工具。

* Detour to build specialized tools

  自己开发工具时，应尽量考虑通用化，而不是针对某个特殊问题进行开发。

## Getting Started

### SHELL脚本的第一行注释

当shell运行一个程序时，会先启动一个新的进程，然后在该进程中执行程序。操作系统知道如何运行编译好的二进制程序，却不知道如何运行shell脚本。所以当操作系统去直接运行shell脚本时会报错："not executable format file"。shell则不会报错，它会先检查该程序是不是二进制程序，如果不是的话就断定它是shell脚本。然后会启动一个新的shell进程去执行脚本。这里便有一个问题，如果系统中有很多不同的shell，如何指定其中一个来运行脚本呢？答案就是在脚本的第一行中使用特殊的注释，如下：

    #! /bin/csh -f

这种机制并不只作用于SHELL脚本，适合于大多数的解释性脚本语言，如awk：

    #! /bin/awk -f
    awk program here

### 基本SHELL组成部分

* 命令和参数

      cd work ; ls -l

  cd是命令，work是参数。同样地，ls是命令，－l是参数。

* 变量

      myvar=this_is_a_long_string_that_does_not_mean_much
      echo $myvar

* 简单的输出

      $ echo Now is the time for all good men
      Now is the time for all good men
      $ echo -n "Enter your name:"
      Enter your name: _

* 功能更强的输出

      printf format-string [arguments ...]

* 基本I/O重定向
  * 使用`<`重定向standard input

        tr -d '\r' < dos-file.txt ...

  * 使用`>`重定向standard output

        tr -d '\r' < dos-file.txt > unix-file.txt

  * 使用`>>`追加内容

        for f in dos-file*.txt
        do
            tr -d '\r' < $f >> big-unix-file.txt
        done

  * 使用`|`建立管道

        tr -d '\r' < dos-file.txt | sort > unix-file.txt

  * 特殊文件：`/dev/null` 以及 `/dev/tty`

    `/dev/null`又被称作`bit bucket`，任何数据写到该文件上都会直接被操作系统丢弃。具体来说，任何程序向该文件写数据，结果都是数据写入成功，但实际上什么事也没有发生。该特殊文件非常有用，例如有时你只在意命令的exit status，而不在乎该程序的任何输出：

        if grep pattern myfile > /dev/null
        then
            ...   # Pattern is there
        else
            ...   # Pattern is not there
        fi

    如果从/dev/null读取数据，会马上得到EOF。

    `/dev/tty`被程序打开时，Unix会自动重定该文件向到与该程序相关的真实的终端上去(physical console or serial port, or pseudoterminal for network and windowed logins)。这在必须从用户输入中获取数据的情景下非常实用，例如密码：

        printf "Enter new password: "    # Prompt for input
        stty -echo                       # Turn off echoing of typed characters
        read pass < /dev/tty             # Read password
        printf "Enter again: "           # Prompt again
        read pass2 < /dev/tty            # Read again for verification
        stty echo                        # Don't forget to turn echoing back on

### 命令搜索路径

用户可以使用如下命令，查看PATH环境变量。操作系统会按照PATH变量中的路径按顺序查找命令并执行。

    $ echo $PATH
    /bin:/usr/bin:/usr/X11R6/bin:/usr/local/bin

如果只想在当前SHELL中改变搜索路径，可以直接对PATH赋值。

    PATH=/new/path:$PATH

单独一个`:`表示当前路径，如下命令表示优先搜索当前目录下的命令

    PATH=:$PATH

如果想对PATH环境变量的修改永久生效，可以修改`~/.profile`文件，添加如下命令：

    export PATH=/new/path:$PATH

### 访问SHELL脚本的输入参数

SHELL脚本的参数(方法的参数)都是使用顺序数字来访问的：

    echo first arg is $1
    echo tenth arg is ${10}

### 简单地跟踪脚本执行过程

脚本开发是很容易出错的，脚本执行的结果与我们预料的往往不一致。通过简单地打开SHELL的"执行跟踪"功能，一般情况下就能找到问题的根源。打开"执行跟踪"功能的命令如下：

    set -x

关闭的命令如下：

    set +x
