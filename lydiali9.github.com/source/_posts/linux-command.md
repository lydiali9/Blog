---
title: linux command
date: 2018-08-23 14:45:21
tags: ['command', 'linux']
categories: ['command']
---

用linux系统开发已经有四年了， 但是有些命令如果不经常用，还是会忘记，那么接下来就把一些常用的命令总结下，希望可以帮助到自己；
`此文是copy csdn的一篇文章`

### cd 切换当前目录

```javascript
cd /root/Docements # 切换到目录/root/Docements
cd ./path          # 切换到当前目录下的path目录中，“.”表示当前目录  
cd ../path         # 切换到上层目录中的path目录中，“..”表示上一层目录

```

### ls 查看文件与目录的命令

```javascript
-l ：列出长数据串，包含文件的属性与权限数据等
-a ：列出全部的文件，连同隐藏文件（开头为.的文件）一起列出来（常用）
-d ：仅列出目录本身，而不是列出目录的文件数据
-h ：将文件容量以较易读的方式（GB，kB等）列出来
-R ：连同子目录的内容一起列出（递归列出），等于该目录下的所有文件都会显示出来
```

* 这些参数也可以组合使用：

```javascript
ls -l #以长数据串的形式列出当前目录下的数据文件和目录
ls -lR #以长数据串的形式列出当前目录下的所有文件
```

### grep 分析一行的信息
分析一行的信息，若当中有我们所需要的信息，就将该行显示出来，该命令通常与管道命令一起使用，用于对一些命令的输出进行筛选加工.

```javascript
grep [-acinv] [--color=auto] '查找字符串' filename
```

它的常用参数如下：

```javascript
-a ：将binary文件以text文件的方式查找数据
-c ：计算找到‘查找字符串’的次数
-i ：忽略大小写的区别，即把大小写视为相同
-v ：反向选择，即显示出没有‘查找字符串’内容的那一行
# 例如：
# 取出文件/etc/man.config中包含MANPATH的行，并把找到的关键字加上颜色
grep --color=auto 'MANPATH' /etc/man.config
# 把ls -l的输出中包含字母file（不区分大小写）的内容输出
ls -l | grep -i file
```

### find 查找的功能
查找的功能非常强大的命令，相对而言，它的使用也相对较为复杂，参数也比较多

```javascript
find [PATH] [option] [action]
 
# 与时间有关的参数：
-mtime n : n为数字，意思为在n天之前的“一天内”被更改过的文件；
-mtime +n : 列出在n天之前（不含n天本身）被更改过的文件名；
-mtime -n : 列出在n天之内（含n天本身）被更改过的文件名；
-newer file : 列出比file还要新的文件名
# 例如：
find /root -mtime 0 # 在当前目录下查找今天之内有改动的文件
 
# 与用户或用户组名有关的参数：
-user name : 列出文件所有者为name的文件
-group name : 列出文件所属用户组为name的文件
-uid n : 列出文件所有者为用户ID为n的文件
-gid n : 列出文件所属用户组为用户组ID为n的文件
# 例如：
find /home/ljianhui -user ljianhui # 在目录/home/ljianhui中找出所有者为ljianhui的文件
 
# 与文件权限及名称有关的参数：
-name filename ：找出文件名为filename的文件
-size [+-]SIZE ：找出比SIZE还要大（+）或小（-）的文件
-tpye TYPE ：查找文件的类型为TYPE的文件，TYPE的值主要有：一般文件（f)、设备文件（b、c）、
             目录（d）、连接文件（l）、socket（s）、FIFO管道文件（p）；
-perm mode ：查找文件权限刚好等于mode的文件，mode用数字表示，如0755；
-perm -mode ：查找文件权限必须要全部包括mode权限的文件，mode用数字表示
-perm +mode ：查找文件权限包含任一mode的权限的文件，mode用数字表示
# 例如：
find / -name passwd # 查找文件名为passwd的文件
find . -perm 0755 # 查找当前目录中文件权限的0755的文件
find . -size +12k # 查找当前目录中大于12KB的文件，注意c表示byte
```

### cp 复制文件

```javascript
-a ：将文件的特性一起复制
-p ：连同文件的属性一起复制，而非使用默认方式，与-a相似，常用于备份
-i ：若目标文件已经存在时，在覆盖时会先询问操作的进行
-r ：递归持续复制，用于目录的复制行为
-u ：目标文件与源文件有差异时才会复制
```

For Example
```javascript
cp -a file1 file2 #连同文件的所有特性把文件file1复制成文件file2
cp file1 file2 file3 dir #把文件file1、file2、file3复制到目录dir中
```

### mv 移动文件、目录或更名
移动文件、目录或更名

```javascript
-f ：force强制的意思，如果目标文件已经存在，不会询问而直接覆盖
-i ：若目标文件已经存在，就会询问是否覆盖
-u ：若目标文件已经存在，且比目标文件新，才会更新
```

For example:

```javascript
mv file1 file2 file3 dir # 把文件file1、file2、file3移动到目录dir中
mv file1 file2 # 把文件file1重命名为file2
```

### rm 删除文件或目录

```javascript
-f ：就是force的意思，忽略不存在的文件，不会出现警告消息
-i ：互动模式，在删除前会询问用户是否操作
-r ：递归删除，最常用于目录删除，它是一个非常危险的参数
```

For example:
```javascript
rm -i file # 删除文件file，在删除之前会询问是否进行该操作
rm -fr dir # 强制删除目录dir中的所有文件
```

### ps 将某个时间点的进程运行情况选取下来并输出

```javascript
-A ：所有的进程均显示出来
-a ：不与terminal有关的所有进程
-u ：有效用户的相关进程
-x ：一般与a参数一起使用，可列出较完整的信息
-l ：较长，较详细地将PID的信息列出
```

For example:
```javascript
ps aux # 查看系统所有的进程数据
ps ax # 查看不与terminal有关的所有进程
ps -lA # 查看系统所有的进程数据
ps axjf # 查看连同一部分进程树状态
```

### kill 终止的进程

```javascript
1：SIGHUP，启动被终止的进程
2：SIGINT，相当于输入ctrl+c，中断一个程序的进行
9：SIGKILL，强制中断一个进程的进行
15：SIGTERM，以正常的结束进程方式来终止进程
17：SIGSTOP，相当于输入ctrl+z，暂停一个进程的进行
```

For example:
```javascript
# 以正常的结束进程方式来终于第一个后台工作，可用jobs命令查看后台中的第一个工作进程
kill -SIGTERM %1 
# 重新改动进程ID为PID的进程，PID可用ps命令通过管道命令加上grep命令进行筛选获得
kill -SIGHUP PID
```

### killall

```javascript
killall [-iIe] [command name]
```

### file判断接在file命令后的文件的基本数据

```javascript
file filename
#例如：
file ./test
```

### tar 对文件进行打包

```javascript
-c ：新建打包文件
-t ：查看打包文件的内容含有哪些文件名
-x ：解打包或解压缩的功能，可以搭配-C（大写）指定解压的目录，注意-c,-t,-x不能同时出现在同一条命令中
-j ：通过bzip2的支持进行压缩/解压缩
-z ：通过gzip的支持进行压缩/解压缩
-v ：在压缩/解压缩过程中，将正在处理的文件名显示出来
-f filename ：filename为要处理的文件
-C dir ：指定压缩/解压缩的目录dir
```

```javascript
压缩：tar -jcv -f filename.tar.bz2 要被处理的文件或目录名称
查询：tar -jtv -f filename.tar.bz2
解压：tar -jxv -f filename.tar.bz2 -C 欲解压缩的目录
```



