# 01.自定义函数

复杂的awk常常可以使用自己定义的函数来简化。调用自定义的函数与调用内置函数的方法一样。 
------------------------------------------------------------------------------------------------------------------
01.自定义函数定义的格式
自定义函数可以在awk程序的任何地方定义。 
function fun_name (parameter_list) {                  // parameter_list是以逗号分隔的参数列表 
body-of-function                                      // 函数体，是awk语句块
} 
------------------------------------------------------------------------------------------------------------------
02. 举例
awk '{ print "sum =",SquareSum($1,$2) } function SquareSum(x,y) { sum=x*x+y*y ; return sum } ' grade.txt 

# 10.数值函数

## 常见awk内置数值函数
int(x)
求出x 的整数部份，朝向0的方向做舍去。
eg: int(3.9)是3，int(-3.9) 是-3。
sqrt(x)
求出x 正的平方根值。
eg: sqrt(4)=2 exp(x) : 求出x 的次方。eg: exp(2) 即是求e*e 。
log(x)
求出x 的自然对数。
sin(x)
求出x 的sine 值，x 是弪度量。
cos(x)
求出x 的cosine 值，x 是弪度量。
atan2(y,x)
求y/x 的arctangent 值，所求出的值其单位是弪度量。
rand()
得到一个随机数(平均分布在0和1之间)。每次执行gawk，rand从相同的seed生成值。
srand(x)
设定产生随机数的seed为x。
如果在第二次运行awk程序时你设定相同的seed值，你将再度得到相同序列的随机数。
如果省略引数x，例如srand()，则当前日期时间会被当成seed。这个方法可使得随机数值是真正不可预测的。
srand()
其值是当次awk_script运行过程中前次srand(x)的设定的seed值x。

## 以下算术函数执行与 C 语言中名称相同的子例程相同的操作：
函数名
说明
atan2( y, x )
返回 y/x 的反正切。
cos( x )
返回 x 的余弦；x 是弧度。
sin( x )
返回 x 的正弦；x 是弧度。
exp( x )
返回 x 幂函数。
log( x )
返回 x 的自然对数。
sqrt( x )
返回 x 平方根。
int( x )
返回 x 的截断至整数的值。
rand( )
返回任意数字 n，其中 0 <= n < 1。
srand( [Expr] )
将 rand 函数的种子值设置为 Expr 参数的值，或如果省略 Expr 参数则使用某天的时间。返回先前的种子值。
 
# IO函数
## fflush

fflush函数
用以刷新输出缓冲区，
如果没有参数，就刷新标准输出的缓冲区，
如果以空字符串为参数，如fflush("")，则刷新所有文件和管道的输出缓冲区。

## printf/sprintf

awk的输出函数printf，基本上和C语言的语法类似。
--------------------------------------------------------------------------------------------
① 格式:
 printf ("输出模板字符串",参数列表)  
--------------------------------------------------------------------------------------------
② 参数列表是以逗号分隔的列表，参数可以是变量、数字值或字符串。
   参数不是用逗号，使用空格隔开时，输出参数时，是没有空格的。
--------------------------------------------------------------------------------------------
③ 输出模板字符串的字符串中必须包含格式控制符，有几个参数就要求有几个格式控制符。
   模板字符串中可以只有格式控制符而没有其它字符。 
--------------------------------------------------------------------------------------------
④ 格式控制符
   %[-][width][.prec]fmt 

   %          标识一个格式控制符的开始，不可省略。 
   -          表示参数输出时左对齐，可省略。 
   width      一个数字，表示参数输出时占用域的宽度，可省略。 
   .prec      prec是一个数值，表示最大字符串长度或小数点右边的位数，可省略。 
   fmt        一个小写字母，表示输出参数的数据类型，不可省略。 
--------------------------------------------------------------------------------------------
⑤ 常见的fmt 
c ASCII字符 
d 整数 
e 浮点数，科学记数法 
f 浮点数，如 123.44 
g 由awk决定使用哪种浮点数转换e或f 
o 八进制数 
s 字符串 
x 十六进制数
--------------------------------------------------------------------------------------------
⑥ 举例: 
echo "65" | awk '{ printf ("%c\n",$0) }'             // 将打印 A 
awk 'BEGIN{printf "%.4f\n",999}'                     //将打印 999.0000 
awk 'BEGIN{printf "2 number:%8.4f%8.2f",999,888}'    // 将打印 2 number:999.0000 888.00
--------------------------------------------------------------------------------------------
sprintf 
函数可以返回格式化的字符串给变量

# 12.字符串 函数


# 15.时间 函数

# 16.一般函数

常见awk内置系统函数 

close(filename)
将输入或输出的文件filename 关闭。
system(command)
此函数允许调用操作系统的指令，执行完毕後将回到awk程序。 
eg: awk 'BEGIN {system("ls")}'

一般函数是：
函数
说明
close( Expression )
用同一个带字符串值的 Expression 参数来关闭由 print 或 printf 语句打开的或调用 getline 函数打开的文件或管道。如果文件或管道成功关闭，则返回 0；其它情况下返回非零值。如果打算写一个文件，并稍后在同一个程序中读取文件，则 close 语句是必需的。
system(Command )
执行 Command 参数指定的命令，并返回退出状态。等同于 system 子例程。
Expression | getline [ Variable ]
从来自 Expression 参数指定的命令的输出中通过管道传送的流中读取一个输入记录，并将该记录的值指定给 Variable 参数指定的变量。如果当前未打开将 Expression 参数的值作为其命令名称的流，则创建流。创建的流等同于调用 popen 子例程，此时 Command 参数取 Expression 参数的值且 Mode 参数设置为一个是 r 的值。只要流保留打开且 Expression 参数求得同一个字符串，则对 getline 函数的每次后续调用读取另一个记录。如果未指定 Variable 参数，则 $0 记录变量和 NF 特殊变量设置为从流读取的记录。
getline [ Variable ] < Expression
从 Expression 参数指定的文件读取输入的下一个记录，并将 Variable 参数指定的变量设置为该记录的值。只要流保留打开且 Expression 参数对同一个字符串求值，则对 getline 函数的每次后续调用读取另一个记录。如果未指定 Variable 参数，则 $0 记录变量和 NF 特殊变量设置为从流读取的记录。
getline [ Variable ]
将 Variable 参数指定的变量设置为从当前输入文件读取的下一个输入记录。如果未指定 Variable 参数，则 $0 记录变量设置为该记录的值，还将设置 NF、NR 和 FNR 特殊变量。
 
打开外部文件（close用法）
	$ awk 'BEGIN{while("cat /etc/passwd"|getline){print $0;};close("/etc/passwd");}'
	root:x:0:0:root:/root:/bin/bash
	bin:x:1:1:bin:/bin:/sbin/nologina
	daemon:x:2:2:daemon:/sbin:/sbin/nologin
	 
逐行读取外部文件(getline使用方法）
	$ awk 'BEGIN{while(getline < "/etc/passwd"){print $0;};close("/etc/passwd");}'
	root:x:0:0:root:/root:/bin/bash
	bin:x:1:1:bin:/bin:/sbin/nologin
	daemon:x:2:2:daemon:/sbin:/sbin/nologin
	 
	$ awk 'BEGIN{print "Enter your name:";getline name;print name;}'
	Enter your name:
	chengmo
	chengmo
	  
调用外部应用程序(system使用方法）
	[chengmo@centos5 ~]$ awk 'BEGIN{b=system("ls -al");print b;}'
	total 42092
	drwxr-xr-x 14 chengmo chengmo     4096 09-30 17:47 .
	drwxr-xr-x 95 root   root       4096 10-08 14:01 ..
	 
	b返回值，是执行结果。
 
## 21.getline

A．getline从整体上来说，应这么理解它的用法：
   当其左右无重定向符 | 或 < 时，getline作用于当前文件，读入当前文件的第一行，给其后跟的变量 var 或$0（无变量）。
   当其左右有重定向符 | 或 < 时，getline则作用于定向输入文件，由于该文件是刚打开，并没有被awk读入一行，只是getline读入。
   那么getline返回的是该文件的第一行，而不是隔行。

B．getline用法大致可分为三大类（每大类又分两小类），即总共有6种用法。
代码如下：
awk  'BEGIN{"cat data.txt"|getline d; print d}' data2.txt     //注意cat 命令两侧的双引号
awk  'BEGIN{"cat data.txt"|getline; print $0}' data2.txt
awk  'BEGIN{getline d < "cat data.txt"; print d}' data2.txt 
awk  'BEGIN{getline < "data.txt"; print $0}' data2.txt
以上四行代码均实现“只打印data.txt文件的第一行”

若打印全部行，用循环
eg. awk 'BEGIN{FS=":";while( getline<"/etc/passwd" > 0 ){print $1}}' data.txt
-------------------------------------------------------------------------------
awk '{getline d; print d"#"$3}' data.txt
awk首先读入第一行，接着处理getline函数，然后把下一行指定给变量d，再先打印d。
例如：
# cat w.c 
root     pts/0     30Jul13  3days  0.63s  0.47s -bash
root     pts/1     02Aug13  4days  0.17s  0.17s -bash
root     pts/2     06Aug13  0.00s  0.82s  0.00s w
root     pts/4     06Aug13  4days  0.79s  0.40s /bin/bash -i

# awk  '{getline d; print d"#"$3}' w.c    
root     pts/1     02Aug13  4days  0.17s  0.17s -bash#30Jul13
root     pts/4     06Aug13  4days  0.79s  0.40s /bin/bash -i#06Aug13
-------------------------------------------------------------------------------
awk  '{getline; print $0"#"$3}' data.txt
awk首先读入第一行接着处理getline函数，然后把下一行指定给$0，现在的$0已经是下一行内容。
例如：
# awk  '{getline; print $0"#"$3}' w.c     #$0是getline得到的行
root     pts/1     02Aug13  4days  0.17s  0.17s -bash#02Aug13
root     pts/4     06Aug13  4days  0.79s  0.40s /bin/bash -i#06Aug13
-------------------------------------------------------------------------------
# awk  '{getline d; print $0"#"$3}' w.c   #$0是awk读到的行
root     pts/0     30Jul13  3days  0.63s  0.47s -bash#30Jul13
root     pts/2     06Aug13  0.00s  0.82s  0.00s w#06Aug13

# awk  '{getline d; print $0 $d}' w.c     #合并两行
root     pts/0     30Jul13  3days  0.63s  0.47s -bashroot     pts/0     30Jul13  3days  0.63s  0.47s -bash
root     pts/2     06Aug13  0.00s  0.82s  0.00s wroot     pts/2     06Aug13  0.00s  0.82s  0.00s w

## 22.next

在awk中，如果调用next，那么next之后的命令就都不执行了。此行文本的处理到此结束，开始读取下一条记录并操作。

实例：
$ cat data   
1000  
naughty 500  
cc 400  
zoer 100  

$ awk '{if(NR==1){next} print $1,$2}' data   
naughty 500  
cc 400  
zoer 100  

比如说我们有一个data文件，存放了每个人的出差费用，想要打印出来。
但是第一行总费用不要打印，那么使用awk的next忽略掉第一行即可。
---------------------------------------------------------------------------------------------------------------------------------
与next相似，getline也是读取下一行数据，但是与next不同的是，next读取下一行之后，把控制权交给了awk脚本的顶部。
但是getline却没有改变脚本的控制，读取下一行之后，继续运行当前的awk脚本，getline执行之后，会覆盖$0的内容。

例子：
$ cat data   
name naughty  
25 shandong  
age 14  
hah,here is test  

$ cat d  
$1=="name"{print $0;getline;print $0;}  
$1=="age"{print $0}  

$ awk -f d data   
name naughty  
25 shandong  
age 14  

例子中，如果遇到行首是name的，就把下一行读取出来并打印。如果行首是age，就仅仅打印本行。
从运行结果也可以看出来，hah,here is test并没有打印出来，因为它没有匹配name或者age开头。

## 23.system

在awk中可以直接执行shell命令。
# touch a  
# touch b  
# cat a.txt   
a  
b  
# awk '{cmd="rm "$0;system(cmd)}' a.txt   
# ls  
a.txt      dd            important    mysql                py       testdata  
daemon.py  Desktop       installer    mysql_install_db.sh  stu  
data       dfadsfadfadf  jdk1.6.0_33  Public               stu.txt  

在awk中组装命令并且最后使用system()来执行，每次读入一个文件名并删除这个文件。
---------------------------------------------------------------------------------------------------------------------------------
下面使用awk中执行命令来创建一些文件。
# awk 'BEGIN{count=10;i=0;while(i<count){cmd="touch "i".txt";i++;system(cmd)}}'  
# ls  
0.txt  4.txt  8.txt      dd         jdk1.6.0_33          py  
1.txt  5.txt  9.txt      Desktop    mysql                stu  
2.txt  6.txt  daemon.py  important  mysql_install_db.sh  stu.txt  
3.txt  7.txt  data       installer  Public               testdata  

## 24.nextfile

7.4.9 The nextfile Statement
The nextfile statement is similar to the next statement. 
However, instead of abandoning processing of the current record, the nextfile statement instructs awk to stop processing the current data file.

Upon execution of the nextfile statement, FILENAME is updated to the name of the next data file listed on the command line, FNR is reset to one, and processing starts over with the first rule in the program. If the nextfile statement causes the end of the input to be reached, then the code in any END rules is executed. An exception to this is when nextfile is invoked during execution of any statement in an END rule; In this case, it causes the program to stop immediately. See BEGIN/END.
The nextfile statement is useful when there are many data files to process but it isn't necessary to process every record in every file. Without nextfile, in order to move on to the next data file, a program would have to continue scanning the unwanted records. The nextfile statement accomplishes this much more efficiently.
In gawk, execution of nextfile causes additional things to happen: any ENDFILE rules are executed except in the case as mentioned below, ARGIND is incremented, and anyBEGINFILE rules are executed. (ARGIND hasn't been introduced yet. See Built-in Variables.)
With gawk, nextfile is useful inside a BEGINFILE rule to skip over a file that would otherwise cause gawk to exit with a fatal error. In this case, ENDFILE rules are not executed. See BEGINFILE/ENDFILE.
While one might think that ‘close(FILENAME)’ would accomplish the same as nextfile, this isn't true. close() is reserved for closing files, pipes, and coprocesses that are opened with redirections. It is not related to the main processing that awk does with the files listed in ARGV.
	NOTE: For many years, nextfile was a gawk extension. As of September, 2012, it was accepted for inclusion into the POSIX standard. See the Austin Group website.
The current version of the Brian Kernighan's awk (see Other Versions) also supports nextfile. However, it doesn't allow the nextfile statement inside function bodies (seeUser-defined). gawk does; a nextfile inside a function body reads the next record and starts processing it with the first rule in the program, just as any other nextfilestatement.

## 25.close

If the same file name or the same shell command is used with getlinemore than once during the execution of an awk program, the file is opened (or the command is executed) only the first time. At that time, the first record of input is read from that file or command. The next time the same file or command is used in getline, another record is read from it, and so on.
This implies that if you want to start reading the same file again from the beginning, or if you want torerun a shell command (rather than reading more output from the command), you must take special steps. What you must do is use the close function, as follows:
close(filename)
or
close(command)
The argument filename or command can be any expression. Its value must exactly equal the string that was used to open the file or start the command--for example, if you open a pipe with this:
"sort -r names" | getline foo
then you must close it with this:
close("sort -r names")
Once this function call is executed, the next getlinefrom that file or command will reopen the file or rerun the command.
close returns a value of zero if the close succeeded. Otherwise, the value will be non-zero.
---------------------------------------------------------------------------------------------------------------------------
用同一个带字符串值的 Expression 参数来关闭由 print 或 printf 语句打开的或调用 getline 函数打开的文件或管道。
如果文件或管道成功关闭，则返回 0；其它情况下返回非零值。
如果打算写一个文件，并稍后在同一个程序中读取文件，则 close 语句是必需的。
---------------------------------------------------------------------------------------------------------------------------

# 17.位操作函数

位操作函数    version3.1后支持

将操作数从double型转换为unsigned long，进行操作后的结果再转换为double型返回。
and(v1, v2)
compl(val)
Lshift(val, count)
Or(v1,v2)
Rshift(val,count)
Xor(v1,v2)

