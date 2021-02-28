---
title: Shell-变量
date: 2021-02-20 21:11:23
tags: Shell
categories: Shell
cover: https://jiajuu.top/2021/02/20/ShellVariable/ShellVariable.jpg
---

# 环境变量

> 顾名思义，系统环境变量就是系统提供的共享变量，针对于所有Shell程序
>
> 存在于shell的配置文件中，linux系统启动时就会加载。

- **系统级环境变量**

  Shell环境加载全局配置文件中的变量，针对所有用户所有Shell程序，全局共享

  **全局配置文件：**

  ```shell
  /etc/profile
  /etc/profile.d/*.sh
  /etc/bashrc
  ```

- **用户界环境变量**

  Shell环境加载个人配置文件中的变量，针对当前用户所有Shell程序，登陆用户独享

   **个人配置文件：**

   ```shell
   /home/用户目录/.bash_profile
   /home/用户目录/.bashrc
   ```



`env`查询当前Shell系统环境变量

`set`查询当前Shell所有变量，包括系统环境变量、自定义变量、函数



**常用的环境变量：**

| 变量名称     | 含义                                                         |
| ------------ | ------------------------------------------------------------ |
| **PATH**     | **和windows环境变量PATH功能相似，设置命令的搜索路径，':'为分割** |
| HOME         | 当前用户主目录`/root`                                        |
| SHELL        | 当前shell解释器类型`/bin/bash`                               |
| **HISTFILE** | **显示当前用户执行命令的历史列表文件`/root/.bash_history`**  |
| PWD          | 显示当前所在路径                                             |
| OLDPWD       | 显示之前的路径                                               |
| HOSTNAME     | 显示当前主机名                                               |
| HOSTTYPE     | 显示主机的架构                                               |
| **LANG**     | **显示当前系统语言环境`zh_CN.UTF-8`**                        |



## 自定义系统环境变量

> 用户进入Shell环境初始化时，会加载全局配置文件`/etc/profile`里面的环境变量
>
> 所以我们可以把所有Shell程序或命令使用的变量全部在`/etc/profile`文件里面自定义

1. 编辑`/etc/profile`全局配置文件

   ```shell
   JAVA_HOME=/usr/local/jdk1.8.0_162
   CLASSPATH=.:$JAVA_HOME/lib
   PATH=$JAVA_HOME/bin:$PATH
   export  JAVA_HOME CLASSPATH PATH
   ```

2. 手动重载全局配置文件，不然不会立即生效的。

   ```shell
   source /etc/profile 
   ```

   



# 自定义变量

- **自定义局部变量**

  只能在一个脚本文件中使用的变量

  `value_name=value`定义变量

  `unset value_name`删除变量

  `$value_name`查询变量的值

  `${value_name}`也是查询变量名的值，能够防止歧异

  ```shell
  echo Hello ${value_name}!!!#把！！！分隔开来
  ```

  **P.S.**

  - 不能以数字开头

  - 等号两侧不要有空格

  - bash环境下，变量默认类型时字符串类型，无法进行数值运算

  - 变量的值有空格需要用""括起来

  - 不能使用Shell关键字作为变量名称

  

- **自定义常量**

  定义以后无法再修改，也叫只读变量

  `readonly varlue_name`定义常量

  

- **自定义全局变量**

  > 举个栗子：现在有两个Shell脚本文件A和B
  >
  > 如果我们在A文件中执行B文件，那么这个时候A就是父Shell环境，B为子Shell环境
  >
  > 那么，子Shell就无法使用父Shell的变量了
  >
  > 这个时候，全局变量就隆重登场了！

  `export value_name1 value_name2`声明全局变量



## 字符串变量

- **字符串变量的定义**

  ```shell
  str1='abc' #强引用，不会解析''内的变量，原样输出
  str2="abc" #弱引用，能够解析""内的变量，强烈推荐
  str3=abc #也能够解析变量，但是不能有空格，不然会被解析为其他命令
  ```

- **获取字符串长度**

  ```shell
  ${#str}
  ```

- **字符串拼接**

  ```shell
  str1=jiajuu
  str2="Hello World"
  str3=${str1}${str2} #无符号拼接
  str4="${str1}${str2}" #双引号拼接，强烈推荐
  str5=${str1}","${str2} #混合拼接
  ```

- **字符串截取**

  | 格式                    | 说明                                              |
  | ----------------------- | ------------------------------------------------- |
  | **${str:start:length}** | **从左侧第0个开始，向右截取2个字符`${str:0:2}`**  |
  | **${str:start}**        | **从左侧第11个开始，向右截取所有字符`${str:11}`** |
  | ${str:0-start:length}   | 从右侧第5个开始，向右侧截取2个字符`${str:0-5:2}`  |
  | ${str:0-start}          | 从右侧第3个开始，向右截取所有字符`${str:0-3}`     |
  | ${str#*chars}           | 截取从左往右第一个e右边的所有字符`${str#*e}`      |
  | ${str##*chars}          | 截取从左往右最后一个e右边的所有字符`${str#*e}`    |
  | ${str%chars*}           | 截取从右往左第一个e左边的所有字符`${str%e*}`      |
  | ${str%%chars*}          | 截取从右往左最后一个e左边的所有字符`${str%e*}`    |



## 索引数组

> Bash Shell中仅支持一维数组，不支持多维数组

- **数组的定义**

  ```shell
  #方式1,例如nums=(5 2 0 13 14 "jiajuu")
  array_name1=(item1 item2 ...)
  
  #方式2，例如str=([1]="jiajuu" [3]="666" [5]="wuhu"),长度仍为3
  array_name2=([index1]=item2 [index2]=item2 ...) 
  ```

- **数组的获取**

  ```shell
  #获取数组其中一个元素
  ${arr[index]}
  
  #获取数组所有元素
  ${arr[@]}
  ${arr[*]}
  
  #获取数组的长度或个数
  ${#arr[@]}
  ${#arr[*]}
  
  #获取数组指定元素的字符长度
  ${#arr[index]}
  ```

- **数组的拼接**

  ```shell
  array_new=($arr1[@] $arr2[@] ...)
  array_new=($arr1[*] $arr2[*] ...)
  ```

- **数组的删除**

  ```shell
  #删除数组指定元素
  unset array_name[index]
  
  #删除整个数组
  unset array_name
  ```

  

# 特殊符号变量

`$n`表示执行脚本文件时传入的参数

> `$0`获取当前脚本的文件名
>
> `$1~$9`获取第1个~第9个传入的参数
>
> `${num}`第十个以上的参数

`$*` `$@`获取执行脚本文件传入的所有参数

**区别：**

- 不使用""括起来的功能时相同的

- 但是使用""括起来以后

  `$*`获取所有参数拼接为一个字符串,`"$1 $2 $3 $4 $5 $6 $7 $8"`

  `$@`获取一组参数列表对象，` "$1" "$2""$3" "$4" "$5" "$6" "$7" "$8"`

  ```shell
  #!/bin/bash
  
  #直接输出所有参数
  echo '使用$*直接输出'$*
  echo '使用$@直接输出'$@
  
  #循环遍历输出所有传入参数
  echo '循环遍历输出$*所有参数'
  for item in "$*"
  do
  	echo $item
  done
  
  echo '循环遍历输出$@所有参数'
  for item in "$@"
  do
  	echo $item
  done
  ```

`$#`获取所有传入参数的个数

`$?`获取上一个Shell命令的退出状态码，或者时函数的返回值

> 每一个Shell命令的执行都有返回值，用于说明命令是否执行成功
>
> 通常来说，0表示成功，非0表示失败

`$$`获取当前Shell环境的进程ID号