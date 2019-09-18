# Shell 基础

Table of Contents
=================

   * [Shell 基础](#shell-基础)
      * [变量](#变量)
         * [PS 终端提示符](#ps-终端提示符)
         * [<g-emoji class="g-emoji" alias="sunny" fallback-src="https://github.githubassets.com/images/icons/emoji/unicode/2600.png">☀️</g-emoji> $@与$*](#sunny-与)
      * [数组](#数组)
      * [函数](#函数)
      * [控制结构](#控制结构)
         * [case](#case)
         * [for](#for)
         * [while、until](#whileuntil)
         * [for/while 逐行处理文件](#forwhile-逐行处理文件)
         * [for/while 逐行处理命令输出](#forwhile-逐行处理命令输出)
      * [here doc](#here-doc)
      * [数据运算](#数据运算)
      * [Shell 解释过程](#shell-解释过程)

## 变量
### PS 终端提示符
* PS1—默认的交互提示符
默认情况下PS1为”\s-\v\$”，显示为:
-bash-3.2$

转义字符代表的信息:
\h--主机名
\u--用户名
\w--当前工作目录的绝对路径 如：/usr/bin
\W--当前工作目录的基址名 如上例中的bin
\t--当前的系统时间：HH:MM:SS（24小时制）
\@--当前的系统时间，12小时制， 如：07:14 PM
\d--如“Wed May 28”格式的日期
\n--换行符
\$?--上以命令执行后的返回值，成功执行返回0，否则返回一个非零的数
\!--该条命令在历史命令中的编号
\#--该条命令在这个shell中的编号

示例:
```bash
PS1="\e[0;32m\u@\h \e[0;33m\t `if [ $? = 0 ]; then echo \e[32m^_^ \w\e[0m; else echo \e[31mO_O \w\e[0m; fi` \e[0;31m\]\n$\e[0;37m"
```

* PS2—多行交互提示符
当我们的输入一个超过一行的命令时，我们可以使用“\”符号将命令分割成几行来执行，当使用了‘\’之后在第二行会出现一个新的提示符
PS2变量也支持PS1中的属性（\h,\t等）以及各种颜色设置。

示例
```bash
sunshine@0101 ~ 11:09 $ sudo apt-get install build-essential \
> libgtk2.0-dev libavcodec-dev libavformat-dev libjpeg62-dev \
> libtiff4-dev cmake libswscale-dev libjasper-dev
```

* PS3—select命令使用的提示符

* PS4—调试输出追踪
PS4变量定义的是在调试状态下执行shell脚本时的提示符
当使用set -x追踪输出时，默认的"++"
通过设置PS4变量就可以改变默认的”++”，输出一些对脚本调试有用的信息。

PS4="$0 $LINENO+: "
$0:脚本的名字
$LINENO:显示当前命令在脚本中的行数

### :sunny: $@与$*
```Bash
#!/usr/bin/env bash
function func1() {
  echo '>> TEST $* OUTPUT: '
  func2 $*
  echo '>> TEST $@ OUTPUT: '
  func2 $@
  echo '>> TEST "$*" OUTPUT: '
  func2 "$*"
  echo '>> TEST "$@" OUTPUT: '
  func2 "$@"
}

function func2() {
  echo '$1 -> '"$1"
  echo '$2 -> '"$2"
  echo '$3 -> '"$3"
}

# Test
func1 "1.1 1.2" "2.1"
```
$*、 $@、 "$*"、 "$@" 作为参数传递的输出：
```md
>> TEST $* OUTPUT:
$1 -> 1.1
$2 -> 1.2
$3 -> 2.1
>> TEST $@ OUTPUT:
$1 -> 1.1
$2 -> 1.2
$3 -> 2.1
>> TEST "$*" OUTPUT:
$1 -> 1.1 1.2 2.1
$2 ->
$3 ->
>> TEST "$@" OUTPUT:
$1 -> 1.1 1.2
$2 -> 2.1
$3 ->
```
总结：
1. $* $@ 无区别，所有参数整体会按照 IFS指定分隔符(默认空格)分割，重新作为参数，
2. "$*" "$@" 加双引号的引用才有意义，"$*"把参数作为整体传递，"$@" 仍按照原有参数传递。

## 数组

## 函数

## 控制结构

### case

### for

* Shell中常用的是： 
```bash
for((i=1;i<=10;i++))
for i in $(seq 10)
for i in `ls` 
for i in $(ls *.txt)
for i in $* 
for i in ${arr[@]}
for i in f1 f2 f3 
for i in {1..10}
for i in *.txt
```

for in语句与` `和$( )合用，利用` `或$( ) 将多行合为一行，实际是合为一个字符串数组。
for num in $(seq 1 100)

* for 循环中的变量
```bash
$ cat for_var.sh 
#! /bin/sh -

file_name=/etc/passwd
var=0
for line in 
do
  ((var ++ ))
done < ${file_name}

echo "var: ${var}"
echo "line: ${line}"

$ ./for_var.sh 
var: 0
line: 
```
```bash
$ cat for_var_02.sh 
#! /bin/sh -

file_name=/etc/passwd
var=0
cat ${file_name} | for line in
do
  ((var ++ ))
done 

echo "var: ${var}"
echo "line: ${line}"

$ ./for_var_02.sh 
var: 0
line: 
```
```bash
$ cat for_var_01.sh 
#! /bin/sh -

file_name=/etc/passwd
var=0
IFS=$'\n'
for line in $( cat ${file_name}  )
do
  ((var ++ ))
done 
IFS=$'\n\t '

echo "var: ${var}"
echo "line: ${line}"

$ ./for_var_01.sh 
var: 39
line: code:x:506:508::/home/code:/bin/bash

$ grep -n "code:x:506:508::/home/code:/bin/bash" /etc/passwd
39:code:x:506:508::/home/code:/bin/bash
```

### while、until
* while 形式
```md
while 命令/条件
do
      语句
done
```
```md
until 命令/条件
do
      语句
done
```
unitl 循环不常用，但如果在等待一个事件的发生时，until就可以很好地发挥作用
```bash
until who | grep "$user" > /dev/null
do
   sleep 30
done
以上代码，实现等待某个用户登录。
```
* while 循环中的变量
```bash
$ cat while_var.sh 
#! /bin/sh -

file_name=/etc/passwd
var=0
while read line 
do
  ((var ++ ))
done < ${file_name}

echo "var: ${var}"
echo "line: ${line}"

$ ./while_var.sh 
var: 39
line: 
```
```bash
$ cat while_var_01.sh 
#! /bin/sh -

file_name=/etc/passwd
var=0

cat ${file_name} | while read line_var
do
  ((var ++ ))
done 

echo "var: ${var}"
echo "line: ${line_var}"

$ ./while_var_01.sh 
var: 0
line: 
```
```bash
$ cat while_var_02.sh 
#! /bin/sh -

file_name=/etc/passwd
var=0
exec 3<"${file_name}"
while read -u3 line 
do
  ((var ++ ))
done 
exec 3<&-

echo "var: ${var}"
echo "line: ${line}"

$ ./while_var_02.sh 
var: 39
line: 
```
注：使用管道，会产生子进程，while循环再子进程中执行，变量的改变，不会影响父进程。

### for/while 逐行处理文件
* cat while read line
```bash
function cat_while_read_line
{
  cat ${INFILE} | while read LINE
  do
    echo "${LINE}" >> ${OUTFILE}
  done
}
```
* while read LINE buttom
```bash
while read LINE
do
  echo "${LINE}" >> ${OUTFILE}
done  <  ${INFILE} 
```
此方法是逐行处理文件中，效率最高的

* for LINE cat FILE
```bash
for LINE in `cat ${INFILE}`
do
  echo "${LINE}" >> ${OUTFILE}
done
```
* while read line FD IN
```bash
exec  3<${INFILE}
while read -u3 LINE
do
    echo "${LINE}" >> ${OUTFILE}
done
exec 3<&-
```
```bash
exec 3<&0
exec 0<${INFILE}
while read LINE
do
    echo "${LINE}" >> ${OUTFILE}
done
exec 0<&3
exec 3<&-
```
read默认从标准输入读取，方法1可能会存在一个问题，即循环体中如果存在从标准输入读取的命令。
会导致本该只有read读取的输入，被该命令读取了。
这里将文件关联到文件描述符3，read从文件描述符3中读取。

### for/while 逐行处理命令输出
```bash
while read LINE
do
  echo "${LINE}" 
done  <  <(command)
```
两个左尖括号之间有空格
<(command) 符号表示执行命令，并把命令输出指向循环底部。

```bash
while read LINE
do
  echo "${LINE}" 
done  <<EOF
$(command)
EOF
```
使用here docment方法重定向输入。

## here doc
shell脚本程序中，向一条命令传递输入的一种特殊方法是使用here文档。
一个here document就是一段带有特殊目的的代码段。

* 格式：
COMMAND <<SpecialString
...
SpecialString

SpecialString用来界定命令序列的范围，是一个特殊的字符序列，用来界定命令序列的范围，可自定义，但不能出现在传递命令的文档内容中。

* 应用
	主要用于有大量文本输入的情况

* 注意
a.某些工具是不能放入here document中运行的。
b.结尾的limit string, 就是here document最后一行的limit string, 必须从第一个字符开始，它的前面不能够有任何前置的空白。
    而在这个limit string后边的空白也会引起异常，空白将会阻止limit string的识别。
c.对于那些使用"here document"，并且非常复杂的任务, 
    最好考虑使用expect脚本语言，这种语言就是为了达到向交互程序添加输入的目的而量身定做的。


## 数据运算

## Shell 解释过程