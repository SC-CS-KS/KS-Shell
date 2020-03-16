# 01.AWK 调用Shell 函数和命令

awk可以用getline或system来调用shell的命令
01.getline
通过在awk内使用管道，可以把shell命令的输出传送给awk
$ awk 'BEGIN{ "date" | getline date; print date; }'
Sun Mar 8 22:21:52 2009

要记得：
awk 'BEGIN{ "date" | getline date; print date;close("date") }'
执行完一个命令，最好立即调用close(command)

awk 'BEGIN {while ( "ls"|getline ls ) print ls;}'
注意：这里不是getline执行了shell命令是"command"中的双引号被解析为执行引号内部的命令，getline只是获得了执行结果的输出而已。
-----------------------------------------------------------------------------------------------------------------------
getline的用法：
getline 是awk里用于输入重定向的一个函数，他可以从标准输入/一个管道/文件读取输入， 而不只是从当前被处理的文件来处理。
他取得输入的下一行并给NF,NR,FNR等内制变量置值，如果找到一条记录则getline返回1，如果到了文件结束(EOF)则返回0。如果错误则返回-1。

getline除了可以通过管道从shell命令里读取数据外，它还可以从标准输入(用"-"指定从标准输入读入。
或者如果命令行没有任何输入文件且不用 重定向符"<"指定文件，默认也是从标准输入读)和文件里读取数据。
如果getline后面没有指定变量,则读取的数据会放到$0里面
$ awk 'BEGIN{ getline; print $0 }'
hello, for note                          <==这一行是从键盘输入的（因为没有任何输入文件，默认键盘为标准输入）
hello, for note                          <==这一行是awk的print $0 输出的

$ cat sed.test
hello

$ awk 'BEGIN{ getline < "sed.test"; print $0 }'
hello

$ awk 'BEGIN{ getline < "-"; print $0 }'
hello
hello

如果读取成功,getline返回1，如果读到文件结束符，getline返回0，如果发生错误，例如打不开文件，getline返回-1。
所以，getline可以用于while循环中：
$ cat grep.test
hello
myboy
third
fourth

$ awk 'BEGIN{ while( getline line < "grep.test" ){ print line } }'
hello
myboy
third
fourth
-----------------------------------------------------------------------------------------------------------------------
02.system
system的调用形式是system(cmd)，system的返回值是cmd的退出状态。如果要获得cmd的输出，就要和getline结合使用。
$ awk 'BEGIN{ while( system("ls -l") | getline line ){ print line } }'
             //这个命令和awk 'BEGIN{while("ls"|getline ls)print ls;}' 结果相同，故双引号是被替换为system，其内容当作cmd被执行。

awk 'BEGIN { while ("ls -l" | getline var) system("echo " var );}'
-----------------------------------------------------------------------------------------------------------------------
Awk中调用shell自定义函数：
#!/bin/sh
function fun_test4()
{
         _message=$1
         if [ "$_message" -ge "0" ];then
              return 0
         elif [ "$_message" -lt "0" ];then
              return 1
         fi
}

export -f fun_test4

awk '
    BEGIN{
      FS="|"
      printf "awk call shell function: "
      _value=system("fun_test4 10")
      print _value
      if(_value == "0")
      {
             print "shell call the function : 10 greater than 0"
      }else{
             print "shell call the function : less than 0" 
      }  
    }
    {}'   sourcedata/pcscard.dat    
exit

注意：
awk中如果需要调用shell函数需要将函数export为系统参数，然后调用的时候用system。
个人感觉还是直接用awk自己定义函数方便。

# 02.AWK 与 Shell 传递参数

第一部分，shell往awk传参
首先得理解下awk的二种变量，和三种参数（变量）赋值形式。
二种变量，这里指内部变量，和自定义变量（本文先不讨论awk的函数局部变量，后续有空补上）
和shell一样，awk也有一些内置的变量，如NR,NF等，变量的赋值了有各自的规则（不再展开，具体看awk文档各变量说明）
自定义变量，通常是为程序需要自身定义的变量，不具有像内置变量那样的作用。

这二类变量的附值，实际上并没多少差别，只是文档中有提到，对内置变量的附值，有可能会得到不期望的结果，
有些是被接收的，有些是不被接受的
（原文：Caution: Using -v to set the values of the built-in variables may lead to surprising results. 
awk will reset the values of those variables as it needs to, possibly ignoring any predefined value you may have given.）
 
awk三种变量附值形式：
1：-v var=text
特点，作用在BEGIN模块之前，命令行位置必需要文件类参数之前，可以更改内置变量，不包括ARGV。
典型用法是shell给awk传递变量。

2：BEGIN模块
特点：awk程序体进行的变量附值 ，其中BEGIN模块的执行在读输入之前，可以更改内置变量。
典型的用法是在没输入的时候运行一段awk程序就写在begin里
 
3：无-v的var=text形式
特点：可以更改内置变量，类同文件类参数，包括ARGV，放在文件类参数前，在文件类参数对应文件开始读入前起作用。
（多文件处理时，可以对不同的文件传递不同的变量，以做不同的处理）

例子一：
# cat -n a1;cat -n b1;cat -n a.awk
1 aa 5
1 bbb 55
1 ccc 555
2 a
2 b
2 c

BEGIN {
   printf "in begin ,n=%d/n", n;
   for (i = 0; i < ARGC; i++)
      printf "/tARGV[%d] = %s/n", i, ARGV[i]
}
{print n,$n}
END   { printf "in end n=%d/n", n; }

# awk -f a.awk -v n=1 n=3  a1 n=2 b1 
in begin ,n=1
        ARGV[0] = awk
        ARGV[1] = n=3
        ARGV[2] = a1
        ARGV[3] = n=2
        ARGV[4] = b1
3 5
3 55
3 555
2 a
2 b
2 c
in end n=2
 
看点：
1：-v，-f等option参数，不列入ARGV
2：-v的参数，要求在文件类参数（包括不加-v的var=text)之前
3：-v的效果，在BEGIN之前
4：n=3,n=2的效果
 
例子二：
比如要用awk计算某些单词在某些文件（列可能不同）出现个数，除了在程序里指定列，还可以在命令行里进行指定，简单而清晰
# cat words1
fqef word4
wfww word3

# cat words2
word4 aaa
word2 fwww
word1 fwwww

# awk '{a[$n]++}END{for(b in a)print b,a[b]}' n=2 words1 n=1 words2
word1 1
word2 1
word3 1
word4 2
----------------------------------------------------------------------------------------------------------------------------
第二部分 awk往shell传参
这个就涉及到进程间通信了，因为shell调用起来的awk实际的执行环境是shell脚本运行进程的一个子进程，
子进程原则上除了继承到自身环境上的变量外，无法对父进程的环境进行访问和修改的，
所以在awk里执行system()变量赋值，也更改不了shell参数。
 
（函数对shell变量更改）通常可以用这么几个办法：
1:用全局变量附值（这种方法不适用于awk，下边有描述）
var=
func(){ var=blabla; };
func

2:用命令替换附值
func(){ echo blabla; };
var=$(func)

3:eval
(注，在这个例子中倒显得有点烦索，其实eval一个经典的用法就是用来脚本之间传递参数用的，毕竟进程间环境无法相互涉及）
var=
fun(){ echo "var=blabla";};
cmd=$(fun)
eval $cmd
echo $var

4:ipc...
unix系最常用的ipc莫过于管道
    func(){ echo "var=blabla" }
    func | read line
    eval $line

下边有个例子，是以前以前用过的一个例子，用的是命名管道：
$ cat -n test.sh
mkfifo t;

a=0;
b=0;
awk 'BEGIN{print "a=1;b=2";print "end";}' >t &

while read c
do
  test "$c" == "end"  && break;
  eval $c
done <t

echo $a,$b;
rm t

注一：以上函数调用时增加参数不影响使用
注二：除了第一个方法：全局变量外，另外三种方法其实也广泛用到其他函数(如awk)更改shell变量值的应用上，
因为subshell所在的子进程并不能更改到父进程的环境表，更改全局变量在这种情况下没效果。

# 20.Shell 传递数组 给 AWK

declare -a refers
refers=([b]="baidu" [g]="google" [s]="soso")
//注意 bash 4.0 以上版本才支持关联数组

awk -v s1="${refers[*]}" -v s2="${!refers[*]}" 'BEGIN{split(s1,s3," ");split(s2,s4," ");for (i=1;i<=length(s4);i++) refers[s4[i]]=s3[i]; print refers["b"],refers["g"],refers["s"]}'
