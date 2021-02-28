---
title: Shell-工作环境加载流程原理
date: 2021-02-21 02:28:07
tags: Shell
categories: Shell
cover: https://jiajuu.top/2021/02/21/ShellEnv/ShellEnv.png
---

# Shell工作环境分类

> 用户进入linux系统就会初始化Shell环境，这个时候会加载全局配置文件和用户个人配置文件中的环境变量
>
> 每个脚本的执行也有其自己的Shell环境

## 交互式Shell&非交互式Shell

| 类型名称      | 含义                                                         |
| ------------- | ------------------------------------------------------------ |
| 交互式Shell   | 与用户进行交互，用户输入，Shell环境立即响应                  |
| 非交互式Shell | 不需要用户参与便执行多个命令，例如我们的Shell脚本，直接执行并返回结果 |

## 登陆Shell&非登陆式Shell

| 类型名称        | 含义                                                   |
| --------------- | ------------------------------------------------------ |
| Shell登陆环境   | 需要用户名/密码登陆的Shell环境                         |
| Shell非登陆环境 | 不需要用户名/密码进入的Shell环境 或 执行脚本文件的环境 |

> P.S. 不同的工作环境加载环境变量的流程不一样

# Shell登陆环境的加载流程

## 登陆环境变量的加载流程

![登陆环境变量的加载流程](https://jiajuu.top/2021/02/21/ShellEnv/LoginProcess.PNG)

## 非登录环境变量的加载流程

![非登录环境变量的加载流程](https://jiajuu.top/2021/02/21/ShellEnv/NonloginProcess.PNG)

# 加载流程的测试、识别

> 脚本文件可以指定具体的Shell环境执行
>
> Shell登陆环境会运行`/etc/profile`
>
> Shell非登陆环境会运行`/当前用户/.bashrc`
>
> 我们只需要在上面两个文件添加两个测试变量
>
> 然后我们在两个不同的Shell脚本中读取测试变量

## 执行登陆环境执行脚本

```shell
sh -l XX.sh #先加载Shell登陆环境初始化环境变量，在执行脚本文件
```
## 执行非登陆环境执行脚本

```shell
bash #加载Shell非登录环境
sh XX.sh #直接执行脚本文件
```

## 识别Shell环境

```shell
echo $0
#输出-bash为Shell登录模式
#输出bash为Shell非登录模式
```

## 切换Shell环境

1. **直接登陆系统，默认就是Shell登录模式**

2. **su切换用户登陆**

   ```shell
   su username --login
   或者
   su username -l
   #切换到指定用户，加载Shell登陆环境变量
   ```
   
3. **bash切换Shell非登录模式**
   
   ```shell
   bash
   ```

4. **以Shell登陆模式执行脚本**

   ```shell
   sh -l XX.sh 或者 sh --login XX.sh
   bash -l XX.sh 或者 bash --login XX.sh
   ```

   