---
title: Shell-输入输出
date: 2021-02-25 15:28:41
tags: Shell
categories: Shell
cover: https://jiajuu.top/2021/02/25/ShellInputOutput/ShellInputOutput.jpg
---


# 标准输入输出

> 标准输入是从键盘读取用户输入数据
>
> 标准输出是从Shell程序产生，并呈现到显示器供用户查看

| 文件名 | 类型                           | 文件描述符fd | 功能                   |
| ------ | ------------------------------ | ------------ | ---------------------- |
| stdin  | standard input标准输入文件     | 0            | 获取键盘的输入数据     |
| stdout | standard output标准输出文件    | 1            | 将正确数据输出到显示器 |
| stderr | standard error标准错误输出文件 | 2            | 将错误信息输出到显示器 |

> 每个Linux命令运行都会打开这三个文件
>
> Shell程序默认会从stdin文件中读取输入数据
>
> Shell程序默认会从stdout文件中输出正确数据
>
> Shell程序默认会从stderr文件中输出错误信息



# 重定向输入输出

> Linux Shell重定向分为输入重定向、输出重定向
>
> <br>
>
> 若改变数据流入的方向，数据从其他地方流入（而非从键盘流入），这就是输入重定向
>
> 若改变数据流出的方向，数据向其他地方流出（而非流向显示器），这就是输出重定向
>
> <br>
>
> 一般我们会重定向到文件中，把命令结果保存起来供日后查阅



| 命令                     | 说明                                                         |
| ------------------------ | ------------------------------------------------------------ |
| `命令 > file`            | 将正确数据重定向输出到file文件中，覆盖方式                   |
| `命令 < file`            | 将输入重定向从file文件中读取数据                             |
| `命令 >> file`           | 将正确数据重定向输出到file文件汇总，追加方式                 |
| `命令 < file1 > file2`   | 从file1文件读取数据，输出数据到file2文件中                   |
| `命令 fd > file`         | 根据指定的文件描述符fd将数据重定向输出到file文件中，覆盖方式 |
| `命令 fd >> file`        | 根据指定的文件描述符fd将数据重定向输出到file文件中，追加方式 |
| `命令 > file fd1 >& fd2` | 将fd1和fd2文件描述符合并输出到文件                           |
| `fd1 <& fd2`             | 将fd1和fd2文件描述符合并从文件读取输入                       |
| `<< tag`                 | 读取终端输入数据，将开始标记tag和结束标记tag之间的内容作为输入<br>标记名tag可以任意 |

> 输出重定向中，`>`为覆盖输出，`>>`为追加输出

```shell
echo "hello" > log.txt #覆盖方式输出到log.txt

echo "hello" >> log.txt #追加方式输出到log.txt

ll aaaa 2>> log.txt #输出错误信息到log.txt

ll aaaa >> log.txt 2>&1 #错误信息、正确数据合并输出到log.txt

wc -l < log.txt #从log.txt读取数据

while read str; do echo $str; done < log.txt #逐行读取log.txt文件

#读取终端输入数据
wc -l << E0F
aa
bb
cc
dd
E0F
```

