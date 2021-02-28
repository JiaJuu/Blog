---
title: Shell-流程控制
date: 2021-02-25 11:10:19
tags: Shell
categories: Shell
cover: https://jiajuu.top/2021/02/25/ShellProcessControl/ShellProcessControl.jpg
---

# if-else语句

## if语法

多行形式

```shell
if	条件
then
	命令
else
	命令
fi
```

单行形式

```shell
if	条件; then 命令; fi
```

## if-else语法

```shell
if	条件
then
	命令
else
	命令
fi
```

## if-elif-else语法

```shell
if	条件1
then
	命令1
elif 条件2
then
	命令2
elif 条件3
then
	命令3
...
else
	命令N
fi
```

## if条件判断句的退出状态

> linux任何命令的执行都有一个退出状态，无论是内置命令、外部文件命令还是自定义的Shell函数
>
> 当它退出即运行结束时，都会返回一个比较小的整数值给调用它的程序
>
> 这个就是命令的退出状态
>
> <br>大多数命令状态都是0代表成功，非0代表失败
>
> 我们常用`$?`获取上一个命令的命令状态
>
> <br>而我们的if语句就是根据逻辑运算符的退出状态码来确定走哪一条语句



# case语句

```shell
case $num in
1)
	echo "one"
	;;
2)
	echo "two"
	;;
3)
	echo "three"
	;;
*)
	;;

esac
```

> 仅支持简单正则表达式

| 格式    | 说明                                                         |
| ------- | ------------------------------------------------------------ |
| `*`     | 表示任意字符串                                               |
| `[abc]` | 表示a、b、c三个字符中任意一个。比如，[15ZH]表示1、5、Z、H四个字符中任意一个 |
| `m-n`   | 表示从m到n的任意一个字符。比如，[0-9]表示任意一个数字，[0-9a-zA-Z]表示字母或数字 |
| `|`     | 表示多重选择，类似于逻辑运算中的或运算。比如abc\|xyz表示匹配字符串“abc”或者"xyz" |



# while语句

多行形式

```shell
while 条件
do
	命令1
	命令2
	...
	continue; #结束当前一轮循环，进入下一轮循环
	break; #结束当前循环
done
```

当行形式

```shell
while 条件; do 命令; done;
```

## 无限循环

```shell
#方式一
while :
do 
	command
done

#方式二
while true
do
	command
done
```



# until语句

> 也是循环语句，但是和while的处理方式是相反的，条件为false的时候才会继续循环，true时会停止循环

```shell
until 条件
do
	command1
done
```



# for语句

多行形式

```shell
#方式1
for var in 1 2 3 4 5
do
	echo "This is number${var}"
done

#方式2
for var in {1..5}
do
	echo "This is number${var}"
done

#方式3
for((i=1;i<10;i++))
do
	echo "This is number${i}"
done

#死循环
for((;;));do ehco "无限循环大法";done;
```

一行形式

```shell
for var in 1 2 3; do echo "This is number${var}"; done;
```

# select语句

> `select in`循环能够进行菜单选择输入，可以增强交互性，它可以显示出带编号的菜单，用户输入不同的编号就能选择不同的菜单，并执行不同功能

```shell
select name in "jia" "juu" "jiajuu"
do
	case $name in
	"jia")
		echo $name
		break
		;;
	"juu")
		echo $name
		break
		;;
	"jiajuu")
		echo $name
		break
		;;
	*)
		echo "你别乱输入=┗|｀O′|┛ 嗷~~"
	esac
done
```

> 注意：
>
> select是死循环，输入空值或输入无效值都不会结束循环，只能通过`break`语句或者`Ctrl+D`来结束循环
>
> 执行过程中，终端会输出`$?`来显示输入选择的菜单编号