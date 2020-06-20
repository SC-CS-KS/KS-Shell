# 15.for/while/until

## for 循环中的变量

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
------------------------------------------------------------------------------------
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
------------------------------------------------------------------------------------
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
------------------------------------------------------------------------------------


## while 循环中的变量

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
------------------------------------------------------------------------------------
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

注：使用管道，会产生子进程，while循环再子进程中执行，变量的改变，不会影响父进程。
------------------------------------------------------------------------------------
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
------------------------------------------------------------------------------------

## 10.for 形式

1、 for((i=1;i<=10;i++))
-------------------------------------------------------------------------------
2、在shell中常用的是： 
   for i in $(seq 10)
   for i in `ls`
   for i in ${arr[@]}
   for i in $* 
   for File in /proc/sys/net/ipv4/conf/*/accept_redirects
   for i in f1 f2 f3 
   for i in *.txt
   for i in $(ls *.txt)

   for in语句与` `和$( )合用，利用` `或$( ) 将多行合为一行的缺陷，实际是合为一个字符串数组。
   for num in $(seq 1 100)

   用for in语句自动对字符串按空格遍历的特性，对多个目录遍历
   LIST="rootfs usr data data2"
   for d in $LIST

   for i in {1..10}
   for i in stringchar {1..10}
-------------------------------------------------------------------------------
3.for的in 列表是可选性，如果省略，shell会遍历整个命令行参数，等价于  for i in "$@"。
如：  for i
      do 
          case $i in
          -f) ...
              ;;
          ...
          esac
      done
---------------------------------------------------------------------------------------------------------------------------

## 11.while 形式

while 命令/条件
do
      语句
done
---------------------------
until 命令/条件
do
      语句
done
---------------------------
while与until的差别在于对条件的退出状态的处理。
只要condition退出成功，while会继续循环，只要condition未成功结束，until则执行循环。
注意：
until语句中，测试条件发生在每次循环结束，所以循环体至少被执行一次。
------------------------------------------------------------------------------- 
1.while :  //将空命令放在while后，即为无限循环

2.unitl 循环不常用，但如果在等待一个事件的发生时，until就可以很好地发挥作用。
until who | grep "$user" > /dev/null
do
   sleep 30
done
以上代码，实现等待某个用户登录。

3.下面的脚本从seq 10中每次循环读入一行，赋值给LINE变量。
while read LINE 
do 
echo $LINE 
done < seq 10

4.也可以将管道放入while循环中，用来重复处理每一行的输入。
cat file |
    while read name rank serial_no
    do
      ...
    done
------------------------------------------------------------------------------- 

## 15.for/while 各种形式性能

在shell用for循环做数字递增的时候发现问题，特列出shell下for循环的几种方法:
1.for i in `seq 1 1000000`;do
   echo $i
   done
用seq 1 10000000做递增，之前用这种方法的时候没遇到问题，因为之前的i根本就没用到百万(1000000)，
因为项目需要我这个数字远大于百万，发现用seq 数值到 1000000时转换为1e+06，
根本无法作为数字进行其他运算，或者将$i有效、正确的取用。
遂求其他方法解决，如下：
-------------------------------------------------------------------------------
2.for((i=1;i<10000000;i++));do
    echo $i
   done
-------------------------------------------------------------------------------
3.
i=1
while(($i<10000000));do
echo $i
i=`expr $i + 1`
done

因为本方法调用expr故运行速度会比第1，第2种慢不少不过可稍作改进。
将i=`expr $i + 1`改为i=$(($i+1))即可稍作速度的提升，不过具体得看相应shell环境是否支持。
-------------------------------------------------------------------------------
4.
for i in {1..10000000};do
echo $i
done
其实选用哪种方法具体还是得由相应的shell环境的支持，达到预期的效果，再考虑速度方面的问题。
---------------------------------------------------------------------------------------------------------------------------------------

## 20.for/while 逐行处理文件

01.cat while read line
function cat_while_read_line
{
  cat ${INFILE} | while read LINE
  do
    echo "${LINE}" >> ${OUTFILE}
  done
 }
------------------------------------------------------------------------
02.while read LINE buttom
while read LINE
do
  echo "${LINE}" >> ${OUTFILE}
done  <  ${INFILE} 
此方法是逐行处理文件中，效率最高的。
------------------------------------------------------------------------
03.for LINE cat FILE
for LINE in `cat ${INFILE}`
do
  echo "${LINE}" >> ${OUTFILE}
done
------------------------------------------------------------------------
04.while read line FD IN
exec  3<${INFILE}
while read -u3 LINE
do
    echo "${LINE}" >> ${OUTFILE}
done
exec 3<&-
read默认从标准输入读取，方法1可能会存在一个问题，即循环体中如果存在从标准输入读取的命令。
会导致本该只有read读取的输入，被该命令读取了。
这里将文件关联到文件描述符3，read从文件描述符3中读取。

exec 3<&0
exec 0<${INFILE}
while read LINE
do
    echo "${LINE}" >> ${OUTFILE}
done
exec 0<&3
exec 3<&-
以上方法，将输入文件关联到标准输出，read就可以直接从标准输入读取，相当于从输入文件中读取。
要注意保存原来的标准输入管理的文件。
这里未解决上面提到的标准输入被循环体中其他命令读取的问题。

以上重定向的方法也可以用于输出重定向，将标准输出重定向到要输出的文件，这样默认输出到标准输出的信息都会写入到输出文件中。
-------------------------------------------------------------------------------------------------------------------------


## 21.for/while 逐行处理命令输出

while read LINE
do
  echo "${LINE}" 
done  <  <(command)
注意：
两个左尖括号之间有空格
<(command) 符号表示执行命令，并把命令输出指向循环底部。
------------------------------------------------------------------------
while read LINE
do
  echo "${LINE}" 
done  <<EOF
$(command)
EOF
使用here docment方法重定向输入。
------------------------------------------------------------------------

