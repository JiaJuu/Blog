---
title: Shell-常用外部命令
date: 2021-02-25 19:35:35
tags: Shell
categories: Shell
cover: https://jiajuu.top/2021/02/25/ShellTool/ShellTool.jpg
---

# wc命令

> 用来对文本进行统计.包括单词个数、行数、字节数

```
wc [options] [文件名]
```

## 参数说明

| options | 含义                 |
| ------- | -------------------- |
| `-c`    | character,统计字节数 |
| `-w`    | word,统计单词数      |
| `-l`    | line,统计行数        |



# cut命令

> 可以切割提取指定列、字符、字节的数据
>
> 可以逐行读入文本,然后按列划分字段并进行提取输出操作

```
cut [options] filename
```

## 参数说明

| options           | 功能                                                         |
| ----------------- | ------------------------------------------------------------ |
| `-f 提取范围`     | 列号,获取第几列                                              |
| `-d 自定义分隔符` | 自定义分隔符,默认为制表符                                    |
| `-c 提取范围`     | 以字符为单位进行分割                                         |
| `-b 提取范围`     | 以字节为单位进行分割。这些字节位置将忽略多字节字符边界，除非也指定了-n标志 |
| `-n`              | 与“-b”选项连用，不分割多字节字符                             |

## 提取范围说明

| 提取范围  | 说明                                                     |
| --------- | -------------------------------------------------------- |
| n-        | 提取指定第n列或字符或字节后面所有数据                    |
| n-m       | 提取指定第n列或字符或字节到第m列或字符或字节中间所有数据 |
| -m        | 提取指定第m列或字符或字节前面所有数据                    |
| n1,n2,... | 提取指定枚举列的所有数据                                 |

## 示例

text.txt

```txt
AA  jia  11  aa
BB  juu  22  bb
CC  jiajuu  33  cc
```

```shell
cut text.txt -d " " -f l,3 #按空格键分割text.txt文件，提取第一列和第三列

cut text.txt -c 1-2 #提取第一个到第二个字符

ehco "jiajuu" | cut -b 1-3 #提取第一个到第三个字节

ehco "jiajuu" | cut -nb 1-3 #提取第一个到第三个字节，多字节不被截断

#切割提取指定单词数据
cat text.txt | grep jiajuu | cut -d " " -f 2

#切割提取bash进程的PID号
ps -aux | grep bash | head -n 1 | cut -d " " -f 8

#切割提取IP地址
ifconfig | grep broadcast | cut -d " " -f 10
```



# sed命令

> sed（stream editor，流编辑器）是Linux的一款非交互流式文本编辑器(vim是交互式文本编辑器)
>
> 可以对文本文件的每一行数据匹配查询之后进行增、删、查、改等操作，支持按正则匹配文本内容

![sed的处理流程](https://jiajuu.top/2021/02/25/ShellTool/SedProcess.PNG)

> sed一次读取一行内存，会把这行放入缓存(模式空间)中
>
> 然后才对该行进行处理，处理完后将缓存区内容发送到终端

```
sed [选项参数] [模式匹配/sed程序命令] [文件名]

#模式匹配，sed会读取每一行数据到模式空间中，接着判断当前行是否符合模式匹配要求，符合要求就会执行sed程序命令，否则不执行
#如果模式匹配为空，则每一行都会执行sed程序命令
```

## 参数说明

| options      | 功能                                                         |
| ------------ | ------------------------------------------------------------ |
| `-e`         | 直接在指令列模式上进行sed动作编辑。它告诉sed将下一个参数解释为一个sed指令<br>只有当命令行上给出多个sed指令才需要使用该选项,一行命令语句可执行多条sed命令 |
| `-i`         | 直接对内容进行修改，不加`-i`默认预览，不对文件做实际修改     |
| `-f`         | 选项后面跟保存sed指令的文件                                  |
| `-n`         | 取消默认输出，sed默认会输出所有文本内容，使用该选项后只显示处理过的行 |
| `-r ruguler` | 使用扩展正则表达式，默认只识别基本正则表达式                 |

## sed程序命令说明

| 命令 | 功能描述                                            |
| ---- | --------------------------------------------------- |
| a    | add新增，`a`后面接字符串，在下一行显示              |
| c    | change更改，更改匹配行的内容                        |
| d    | delete删除，删除匹配内容                            |
| i    | insert插入，向匹配行前插入内容                      |
| p    | print打印，打印出匹配的内容，通常与`-n`选项一起使用 |
| s    | substitute替换，替换掉匹配的内容                    |
| =    | 用来打印被匹配行的行号                              |
| n    | 读取下一行，遇到n会自动跳入下一行                   |

## 特殊符号

| 命令                  | 功能描述                                       |
| --------------------- | ---------------------------------------------- |
| `!`                   | 取反，放在限制条件后面，对指定行以外所有行应用 |
| `{sed命令1;sed命令2}` | 多个命令操作同一行                             |
| `$`                   | 最后一行                                       |

## 示例

text.txt

```
ABC
jiajuu jiajuu
jia
123
juu
```

```shell
#添加文件中数据
sed '3ahello' text.txt #在第三行后面添加一行，实际上没有修改文件，只是预览
sed -i '3ahello' text.txt #在第三行后面添加一行，修改文件
sed '3ihello' text.txt #在第三行前面添加一行，实际上没有修改文件，只是预览

sed '/jiajuu/ihello' text.txt #在jiajuu指定字符串前添加一行
sed '/jiajuu/ahello' text.txt #在jiajuu指定字符串后添加一行

sed '$ahello' text.txt #在最后一行后面添加一行
sed '$ihello' text.txt #在最后一行前面添加一行

#删除文件中数据
sed '2d' text.txt #删除第二行
sed '1~2d' text.txt #删除奇数行，1代表第一行，2代表每隔两行
sed '1,3d' text.txt #删除第一行到第三行
sed '1,3!d' text.txt #删除第一行到第三行外全部行
sed '$d' text.txt #删除最后一行
sed '/jiajuu/d' text.txt #删除含有jiajuu的行
sed '/jiajuu/,$d' text.txt #删除含jiajuu行到最后一行
sed '/jiajuu/,+1d' text.txt #删除含jiajuu行和下一行
sed '/jia\|juu/!d' text.txt #删除含有jia和juu行以外所有行

#更改文件中数据
sed '1chello' text.txt #修改第一行为hello
sed '/jiajuu/chello' text.txt #修改含jiajuu行为hello
sed '$chello' text.txt #修改最后一行为hello
sed 's/jiajuu/hello/' text.txt #将每行的第一个jiajuu替换为hello
sed 's/jiajuu/hello/g' text.txt #将每行的所有jiajuu替换为hello
sed 's/jiajuu/hello/2' text.txt #将每行的第二个jiajuu替换为hello
sed 's/jiajuu/hello/2w temp.txt' text.txt #将替换的内容行写入文件
sed -n 's/jiajuu/hello/2p' text.txt #显示替换的内容，n是获取匹配的，p是打印
sed '/i/s/a.*//' text.txt #正则表达式匹配替换，匹配有i的行，替换匹配行中a后面所有内容为空
sed 's/$/& test/' text.txt #每行的末尾拼接test，&为拼接
sed 's/^/&#/' text.txt #每行的行首添加#，&为拼接

#查询文件中数据
sed -n '/jiajuu/p' #查询含有jiajuu的行
ps -aux | sed -n '/sshd/p' #管道查询所有进程中含有sshd的进程信息命令

#多个sed命令执行
sed -e '1d' -e 's/jiajuu/hello/g' text.txt #方式一，将text.txt文件中的第一行删除并将jiajuu替换为hello
sed '1d;s/jiajuu/hello/g' text.txt #方式2，将text.txt文件中的第一行删除并将jiajuu替换为hello
```

## 高级用法：缓存区数据交换

> 模式空间与暂存空间：
>
> - sed处理文件是逐行处理的，即读取一行处理一行，输出一行
>
> - ssd把文件读出来的每一行存放的空间为模式空间，会在该空间中对读取的内容做相应的处理
>
> - sed还有一个额外的空间为暂存空间，暂存空间刚才是只有一个空行
>
> - sed可使用相应的命令从模式空间往暂存空间放入内容，或者从暂存空间内容放入模式空间
>
>   > 模式空间与暂存空间的目的时为了更好的处理数据，实现复制、粘贴。

### 缓存区命令

| command | 含义                                                   |
| ------- | ------------------------------------------------------ |
| `h`     | 将`模式空间`里面的内容复制到`暂存空间`缓存区(覆盖方式) |
| `H`     | 将`模式空间`里面的内容复制到`暂存空间`缓存区(追加方式) |
| `g`     | 将`暂存空间`里面的内容复制到`模式空间`缓存区(覆盖方式) |
| `G`     | 将`暂存空间`里面的内容复制到`模式空间`缓存区(追加方式) |
| `x`     | 交换2个空间的内容                                      |

### 示例

```shell
#缓存空间数据交换
#将模式空间第一行复制到暂存空间(覆盖方式)，并将暂存空间的内容复制进模式空间中的最后一行(追加方式)
sed '1h;$G' text.txt #将第一行复制到最后一行

#将模式空间第一行复制到暂存空间(覆盖方式)并删除，最后将暂存空间的内容复制到模式空间中的最后一行(追加方式)
sed '1{h;d};$G' text.txt #第一行删除后粘贴到最后一行

#将模式空间第一行复制到暂存空间(覆盖模式)，最后将暂存空间的内容复制到模式空间中替换从第二行带式到最后一行的每一行数据
sed '1h;2,$g' text.txt #将第一行复制粘贴替换第二行到最后一行

#将前三行数据复制到暂存空间(追加方式)，之后将暂存空间的所有内容复制到模式空间复制粘贴到模式空间最后一行(追加方式)
sed '1,3H;$G' text.txt #将前三行数据复制粘贴到最后一行

sed 'G' text.txt #给每一行添加空行

sed '/^$/d' text.txt #删除空行，^开始，$结尾
```



# awk命令

> `awk`能够分析文本
>
> 相对于`grep`的查找，`sed`的编辑，`awk`在对数据分析并生成报告尤为强大简单
>
> `awk`就是把文件逐行读取，以空格为默认分隔符为每行切片，切开部分再进行分析处理
>
> 且`awk`能够定义变量，使用运算符，使用流程控制语句

```shell
awk [options] 'pattern{action}' {filename}
```

>pattern：表示`awk`再数据中查找的内容，也就是匹配模式
>
>action：再找到匹配内容时所执行的命令

## 参数说明

| options | 功能                   |
| ------- | ---------------------- |
| `-F`    | 指定输入文件拆分分隔符 |
| `-v`    | 赋值一个用户定义变量   |

## 内置变量说明

| 内置变量   | 含义                                                         |
| ---------- | ------------------------------------------------------------ |
| `ARGC`     | 命令行参数个数                                               |
| `ARGV`     | 命令行参数排列                                               |
| `ENVIRON`  | 支持队列中系统环境变量的使用                                 |
| `FILENAME` | awk浏览的文件名                                              |
| `FNR`      | 浏览文件的记录数                                             |
| `FS`       | 设置输入域分隔符，等价于命令行 -F选项                        |
| `NF`       | 浏览记录域的个数，根据分隔符分割后的列数                     |
| `NR`       | 已读的记录数，也就是行号                                     |
| `OFS`      | 输出域分隔符                                                 |
| `ORS`      | 输出记录分隔符                                               |
| `RS`       | 控制记录分隔符                                               |
| `$n`       | `$0`变量是指整条记录。`$1`表示当前行的第一个域...            |
| `$NF`      | `$NF`是number finally，表示最后一列的信息，注意：变量NF是统计每行列的总数 |

## 示例

```shell
echo "abc 123 456" | awk '{print $1"&"$2"&"$3}' #用"&"分割字符串

awk '/root/{print $0}' passwd #打印含有'/root'的行

awk -F ":" '/root/{print $7}' passwd #以":"拆分匹配行，打印匹配行中第七列

#打印文件每行属性信息
awk -F ":" '{print "文件名："FILENAME",行号："NR"，列数："NF"，内容："$0}' passwd 
awk -F ":" '{printf("文件名：%s,行号：%s,列数：%s,内容：%s"，FILENAME,NR,NF,$0)}' passwd

awk -F ":" 'NR==2{printf("filename:%s,内容:%s\n",FILENAME,$0)}' passwd #打印第二行信息

ls -a | awk '/^c/' #查找以c开头的文件

awk -F ":" '{print $1}' passwd #打印第一列
awk -F ":" '{print $NF}' passwd #打印最后一列
awk -F ":" '{print $(NF-1)}' passwd #打印最后第二列
awk -F ":" '{if(NR>=10 && NR<=20){print $1}}' passwd #打印10到20行的第一列

echo "one:two/three" | awk -F "[:/]" '{print $1" "$2" "$3}' #多分隔符分割

#添加开始与结束内容
echo -e "abc\nabc" | awk 'BEGIN{print "开始..."}{print $0}END{print "结束..."}'

echo "abc  jiajuu jia juu" | awk '{print $1" "$2" "$3}' #拼接分割的字符串

#使用循环拼接分割的字符串
echo "abc  jiajuu jia juu" | awk -v str="" '{for(n=1;n<=NF;i++){str=str$n}} END{print str}'

echo "2.1" | awk -v i=1 '{print $0+i}' #操作指定数字运算

ifconfig | awk '/broadcast/{print $0}' | awk '{print $2}' #切割打印ip地址

sed 'G' text.txt | awk '/^$/{print NR}' #显示空行行号
```



# sort命令

> 能对文件进行排序，并将结果标准输出或重定向输出到指定文件

```
sort (options) 文件列表
```

## 参数说明

| options       | 说明                                                 |
| ------------- | ---------------------------------------------------- |
| `-n`          | number，依照数值大小排序                             |
| `-r`          | reverse，以相反顺序排序                              |
| `-t 分隔字符` | 设置排序是所用的分隔字符，默认空格                   |
| `-k`          | 指定需要排序的列                                     |
| `-d`          | 排序时，处理英文字母、数字及空格字符外，忽略其他字符 |
| `-f`          | 排序时，将小写字母视为大写字母                       |
| `-b`          | 忽略每行前面开始的空格字符                           |
| `-o 输出文件` | 将排序后的结果存入指定的文件                         |
| `-u`          | unique，输出的结果是去重的                           |
| `-m`          | 将几个排序号的文件合并                               |

## 示例

text.txt

```txt
A 张三 30
A 李四 55
C jiajuu 100
C jia 95
B juu 98
B jia 90
```

```shell
sort -t " " -k3n,3 text.txt #按第三列升序排序

sort -t " " -k3nr,3 text.txt #按第三列降序排序

sort -t " " -k3n,3 -uk2,3 text.txt #按第二列以及第三列去重

sort -t " " -k3n,3 -uk2,3 -o temp.txt text.txt #将输出结果保存到temp.txt中

sort -t "," -k1,1 -k3nr,3 text.txt #多列排序，以"，"分割先对第一列字符串升序排序，再对第三列数字降序排序
```

