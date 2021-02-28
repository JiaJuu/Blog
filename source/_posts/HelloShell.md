---
title: Shell-入门概述
date: 2021-02-19 22:43:56
tags: Shell
categories: Shell
cover: https://jiajuu.top/2021/02/19/HelloShell/HelloShell.png
---


# 概述

- linux是通过内核接口方式控制计算机的硬件

- 而程序猿通过shell命令的方式让内核去操作计算机的硬件

- 一系列的shell命令编写的shell文本，便是我们常说的shell脚本

# Shell运行流程

![Shell运行流程](https://jiajuu.top/2021/02/19/HelloShell/ShellProcess.PNG)

# Shell解析器

`cat /etc/shells`查询linux支持的Shell解释器

`echo $shell`查询现在默认使用的Shell解释器，其中$shell是全局共享的环境变量

| 常见解释器    | 介绍                                                         |
| ------------- | :----------------------------------------------------------- |
| /bin/sh       | UNIX最初使用的shell                                          |
| **/bin/bash** | **linux默认的解释器**                                        |
| /sbin/nologin | 未登录解释器，用于控制用户禁止登陆系统的，针对部分不需要登陆的服务（例如邮件服务） |
| /bin/csh      | C语言风格的shell                                             |

# Shell脚本文件

`.sh`Shell脚本文件后缀常

`#!/usr/bin/bash`首行设置默认Shell解释器，声明该脚本文件所采用的解释器

`# 注释内容`单行注释

```shell
:<<!
多行注释1
多行注释2
多行注释3
！
```

## Shell脚本文件执行

`sh HelloWorld.sh`sh解释器执行，

`bash HelloWorld.sh`bash解释器执行

`./HelloWorld.sh`路径执行，文件需要可执行权限

**区别：**

- sh&bash执行脚本是直接使用解释器运行脚本文件，无需可执行权限
- 路径执行需要可执行权限，且采用的解释器为脚本文件的首行声明解释器

## Shell脚本多命令执行

```
#!/bin/bash
:<<!
1.在/home/jiajuu路径下创建test目录
2.在/home/jiajuu/test路径下创建hello.txt文件
3.输出Hello Shell到test01.sh文件中
!
mkdir /home/jiajuu/test
touch /home/jiajuu/test/test01.sh
echo "Holle Shell" >> /home/jiajuu/test/hello.txt

```

