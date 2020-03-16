# 01.AWK 拼接Shell命令

ps aux|grep ping |awk  '{print "kill -9  " $2 }' | sh
杀死 执行命令行中含 ping 字符的进程 

注:
1.命令拼接时的空格符号。
2.如果ping这类不会自动结束的命令，shell应该是会等待，不会再执行后面行的命令。

如：
# arp | tail +2 |awk '{ print "ping " $1}' |sh

只会执行 ping 第一个IP的命令，可以用ping命令的-c参数来限定执行的次数。

# 02.AWK 控制行的打印

1.通过NR变量来控制打印的行
-------------------------------------------------
删除最后一行，即不打印最后一行：
awk -F : 'NR>1 {print p} {p=$2}' file          #注意：p=$2的花括号不能丢

以上想法很巧妙：
当NR=1时，不符合NR>1的条件，将第一行的第二个字段保存在p变量中。
当NR=2时，符合条件，打印p，此时p是第一行的第二个字段的数据，然后再把第二行的第二个字段保存在p中。
如此每次打印前一个记录中的数据，直到最后一行
当NR到达最后一行时，打印倒数第二行中的记录，然后把最后一行的第二个字段保存。
但是awk已经处理完所有的行，所以最后一行不会被打印。
-------------------------------------------------
可以通过NR的表达式，来实现只打印前n行
NR<n+2

实现打印第n行 以后的行，但不包括最后一行
NR>n
------------------------------------------------------------------------------------------------------------------------------
2.通过if和NR来控制
awk '{ a[NR]=$0} END{ for(i=1;i<NR;i++) print a[i]}'
上面实现方法主要要得到文本的总行数，同时把文本行存入数组，在END中控制和打印。
------------------------------------------------------------------------------------------------------------------------------
3.打印最后一行
awk 'END {print}' passwd.t 

# 03.AWK 处理多个文件

awk的数据输入有两个来源，标准输入和文件，后一种方式支持多个文件。
如：
1. shell的Pathname Expansion方式：
awk '{...}'  *.txt
#  *.txt先被shell解释，替换成当前目录下的所有*.txt，
#  如当前目录有1.txt和2.txt，则命令最终为awk '{...}' 1.txt 2.txt

2. 直接指定多个文件： 
awk '{...}' a.txt b.txt c.txt ... 
# awk对多文件的处理流程是，依次读取各个文件内容，如上例，先读a.txt，再读b.txt....

那么，在多文件处理的时候，如何判断awk目前读的是哪个文件，而依次做对应的操作呢？

########################
#     处理 2 个文件
########################
当awk读取的文件只有两个的时候，比较常用的有两种方法：
(1) 一种是
awk 'NR==FNR{...}NR>FNR{...}'  file1 file2
或
awk 'NR==FNR{...}NR!=FNR{...}' file1 file2

(2) 另一种是 awk 'NR==FNR{...;next}{...}' file1 file2

了解了FNR和NR这两个awk内置变量的意义就很容易知道这两种方法是如何运作的
FNR     The input record number in the current input file.  #已读入当前文件的记录数
NR      The total number of input records seen so far.      #已读入的总记录数

next    Stop processing the current input record. The next input record is read and processing starts over with the first pattern in the AWK
        program. If the end of the input data is reached, the END block(s),  if any, are executed.

awk 'NR==FNR{...}NR>FNR{...}' file1 file2
# 读入file1的时候，已读入file1的记录数FNR一定等于awk已读入的总记录数NR，因为file1是awk读入的首个文件，故读入file1时执行前一个命令块{...}
# 读入file2的时候，已读入的总记录数NR一定>读入file2的记录数FNR，故读入file2时执行后一个命令块{...}


awk 'NR==FNR{...;next}{...}' file1 file2
# 读入file1时，满足NR==FNR，先执行前一个命令块，但因为其中有next命令，故后一个命令块{...}是不会执行的
# 读入file2时，不满足NR==FNR，前一个命令块{..}不会执行，只执行后一个命令块{...}

########################
#     处理 多个 文件
########################
当awk处理的文件超过两个时，显然上面那种方法就不适用了。
因为读第3个文件或以上时，也满足NR>FNR (NR!=FNR)，显然无法区分开来，所以就要用到更通用的方法了：
1. ARGIND        # 当前被处理参数标志
awk 'ARGIND==1{...}ARGIND==2{...}ARGIND==3{...}... ' file1 file2 file3 ...

2. ARGV            # 命令行参数数组
awk 'FILENAME==ARGV[1]{...}FILENAME==ARGV[2]{...}FILENAME==ARGV[3]{...}...' file1 file2 file3 ...

3. 把文件名直接加入判断
awk 'FILENAME=="file1"{...}FILENAME=="file2"{...}FILENAME=="file3"{...}...' file1 file2 file3 ...

########################
#        例子 1
########################
现有file1,file2 两个文件。文件file1有2列，内容如：
no1 name1
no2 name2
no3 name2
no4 name3
no5 name4
no6 name4
no7 name4
no8 name5
no9 name6
no10 name6

文件file2 有6列，部分有空格，内容如下：
name1 data1 dada2 data3 data4 dada5
name2 dada6 data7 dada8
name3 data9 dada10 data11 dada12
name4 data13 dada14
name5 data15 dada16
name6 data17 data18

如果file1的第2列跟file2的第1列匹配，则将两条数据合并成一条，合并后的数据应该是这样的：
no1 name1 data1 dada2 data3 data4 dada5
no2 name2 dada6 data7 dada8
no3 name2 dada6 data7 dada8
no4 name3 data9 dada10 data11 dada12
no5 name4 data13 dada14
no6 name4 data13 dada14
no7 name4
no8 name5 data15 dada16
no9 name6 data17 data18
no10 name6 data17 data18

程序：
awk 'NR==FNR{a[$1]=$0}NR>FNR{print $1" "a[$2]}' file2 file1

########################
#        例子 2
########################
file1：
sina.com 52.5
sohu.com 42.5
baidu.com 35

file 2：
www.news.sina.com sina.com 80
www.over.sohu.com baidu.com 20
www.fa.baidu.com sohu.com 50
www.open.sina.com sina.com 60
www.sport.sohu.com sohu.com 70
www.xxx.sohu.com sohu.com 30
www.abc.sina.com sina.com 10
www.fa.baidu.com baidu.com 50
www.open.sina.com sina.com 60
www.over.sohu.com sohu.com 20

合并的结果：
www.news.sina.com sina.com 80 52.5
www.over.sohu.com baidu.com 20 42.5
www.fa.baidu.com sohu.com 50 35
www.open.sina.com sina.com 60 52.5
www.sport.sohu.com sohu.com 70 42.5
www.xxx.sohu.com sohu.com 30 42.5
www.abc.sina.com sina.com 10 52.5
www.fa.baidu.com baidu.com 50 35
www.open.sina.com sina.com 60 52.5
www.over.sohu.com sohu.com 20 42.5

程序：
awk 'NR==FNR{a[$1]=$2;next}{print $0,a[$2]}' file1 file2

# 50.AWK 合并文件

假设有两个文件是这样的：
# cat id_name
1 naughty
2 cc
4 yy
3 zoer

# cat id_score 
1 11
2 22
3 33
4 44
现在要求使用shell命令，做一份报告，报告格式：学生姓名+学生成绩，中间以空格分隔。
-------------------------------------------------------------------------------------------------------------
# sort id_name >a
# sort id_score >b
# cat a
1 naughty
2 cc
3 zoer
4 yy
# cat b
1 11
2 22
3 33
4 44
# join a b>c
# cat c
1 naughty 11
2 cc 22
3 zoer 33
4 yy 44
# awk '{print $2," ",$3}' c
naughty   11
cc   22
zoer   33
yy   44
上面列出了实现过程。首先对两个文件按照学号排序。排好序之后使用join命令将两个文件连接起来，然后使用awk输出后两列即可。
-------------------------------------------------------------------------------------------------------------
上面是使用了join命令来帮忙。如果不使用join呢？看下面
#cat id_name id_score |sort -k 1 |
awk 'BEGIN{t=""}{t=$1;if(tt==t){printf(" %s",$2);printf("\n")}else{printf("%s %s",$1,$2);}tt=t}'|awk '{print $2," ",$3}'
11   naughty
22   cc
33   zoer
44   yy

先排序，排序之后，相同id的学生信息都放在一起了。
然后使用了两个awk来合并结果。【如果这里第一个awk的使用方法不明白，文章末尾还有一个例子来说明这个用法】

--》》》》》

上面的代码修改一下print，就可以只用一个awk命令了 。
# cat id_name id_score |sort -k 1 |awk 'BEGIN{t=""}{t=$1;if(tt==t){printf(" %s",$2);printf("\n")}else{printf("%s",$2);}tt=t}'
11 naughty
22 cc
33 zoer
44 yy
-------------------------------------------------------------------------------------------------------------
再看一个awk的例子。去掉d文件中多余的第一个字段。
# cat d
m naughty
m cc
n zoer
n yy
n zz

# awk 'BEGIN{t=""}{t=$1;if(tt!=t)print $1,"\t",$2;else{print "\t",$2;}tt=t;}' d
m   naughty
      cc
n   zoer
     yy
     zz

上面的代码去掉了d文件中不必要的第一个字段。使用了记录第一个字段并且每次处理一行的时候比较第一个字段的技巧。

# 51.AWK 乱序排列文件

洗牌问题：
洗一副扑克，有什么好办法？既能洗得均匀，又能洗得快？即相对于一个文件来说怎样高效率的实现乱序排列？

ChinaUnix 确实是 Shell 高手云集的地方，只要你想得到的问题，到那里基本上都能找到答案。
r2007 给出了一个取巧的方法，利用 Shell 的 $RANDOM 变量给原文件的每一行加上随机的行号然后根据这个随机行号进行排序。
再把临时加上去的行号给过滤掉，这样操作之后得到的新文件就相当于被随机“洗”了一次：
while read i;
do echo "$i $RANDOM";
done   <file  |  sort -k2n | cut -d" " -f1

当然如果你的源文件每行的内容比较复杂的话就必须对这段代码进行改写，但只要知道了处理的关键技巧，剩下的问题都不难解决。
-----------------------------------------------------------------------------------------------------------------------
另外一篇来自苏蓉蓉的用 awk 来实现洗牌效果的随机文件排序代码分析则写的更为详细：

关于洗牌问题，其实已经有了一个很好的Shell解法，这里另外给三个基于AWK的方法，有错误之处还请不吝指出。
方法一：穷举
类似于穷举法，构造一个散列来记录已经打印的行出现的次数，如果出现次数多于一次则不进行处理，
这样可以防止重复，但缺点是加大了系统的开销。
awk -v N=`sed -n '$=' data`
'BEGIN{ FS="\n"; RS=""}
{  srand(); 
   while(t!=N){
     x=int(N*rand()+1);
     a[x]++;
     if(a[x]==1)
     {
        print $x;
        t++
     }
  }
}
' data
------------------------------------------------------------------------------------------------------
方法二：变换
基于数组下标变换的办法，即用数组储存每行的内容，通过数组下标的变换交换数组的内容，效率好于方法一。

#! /usr/awk

BEGIN{
srand();
}

{
b[NR]=$0;
}

END{

C(b,NR);
for(x in b)
  {
    print b[x];
  }}

function C(arr,len,i,j,t,x){

for(x in arr)
  {
      i=int(len*rand())+1;
      j=int(len*rand())+1;
      t=arr[i];
      arr[i]=arr[j];
      arr[j]=t;
  }

}
------------------------------------------------------------------------------------------------------
方法三：散列
三个方法中最好的。
利用AWK中散列的特性(详细请看：info gawk 中的7.x ),只要构造一个随机不重复的散列函数即可，
因为一个文件每行的linenumber是独一无二的，所以用：
随机数＋每行linenumber    ------对应------>    那一行的内容即为所构造的随机函数。
从而有：
awk 'BEGIN{srand()}
       {b[rand()NR]=$0}
       END{for(x in b)print b[x]}' data
-------------------------------------------------------------------------------------------------------------------------
其实大家担心的使用内存过大的问题不必太在意，可以做一个测试：
测试环境：
PM 1.4GHz CPU,40G硬盘，内存256M的LAPTOP
SUSE 9.3  GNU bash version 3.00.16 GNU Awk 3.1.4

产生一个五十几万行的随机文件,大约有38M：
od /dev/urandom | dd  count=75000 >data

拿效率较低的方法一来说，洗牌一次所用时间:
time awk -v N=`sed -n '$=' data` '
BEGIN{
FS="\n";
RS=""
}
{
srand();
while(t!=N){
  x=int(N*rand()+1);
  a[x]++;
  if(a[x]==1)
    {
        print $x;t++
    }
  }
}
' data

结果（文件内容省略）：
real    3m41.864s
user    0m34.224s
sys     0m2.102s

所以效率还是勉强可以接受的。

方法二的测试:
time awk -f awkfile datafile

结果（文件内容省略）：
real    2m26.487s
user    0m7.044s
sys     0m1.371s

效率明显好于第一个。

接着考察一下方法三的效率：
time awk 'BEGIN{srand()}{b[rand()NR]=$0}END{for(x in b)print b[x]}' data

结果（文件内容省略）：
real    0m49.195s
user    0m5.318s
sys     0m1.301s

对于一个38M的文件来说已经相当不错了。
-------------------------------------------------------------------------------------------------------------------------
附带存一个来自 flyfly 写的 python 版本乱序代码：
#coding:gb2312 
import sys 
import random 

def usage(): 
print "usage:program srcfilename dstfilename" 
global filename 
filename = "" 
try: 
filename = sys.argv[1] 
except: 
usage() 
raise() 
#open the phonebook file 

f = open(filename, 'r') 
phonebook = f.readlines() 
print phonebook 
f.close() 

#write to file randomly 
try: 
filename = sys.argv[2] 
except: 
usage() 
raise() 

f = open(filename, 'w') 
random.shuffle(phonebook) 
f.writelines(phonebook) 
f.close()

# 90.删除注释语句
```bash
#!/bin/awk -f
{
     if (t = index($0, "/*")) {
          if (t > 1)
               tmp = substr($0, 1, t - 1)
          else
               tmp = ""
          u = index(substr($0, t + 2), "*/")
          while (u == 0) {
               getline
               t = -1
               u = index($0, "*/")
          }
          if (u <= length($0) - 2)
               $0 = tmp substr($0, t + u + 3)
          else
               $0 = tmp
     }
     print $0
}
```

# 91.获取水木中关村的租房信息
```bash
awk 'BEGIN{ 
command="wget 'http://www.newsmth.net/bbsdoc.php?board=HouseRent' -O a.txt -q";
system(command);
while(getline a < "a.txt") 
{
  if (a ~/.*中关村.*/) print a
}
}'
```
# 92.进行url编解码
```bash
进行url编码
od -t x "a.txt" | awk '{
w=split($0,linedata," ");
for (j=2;j<w+1;j++)
{
for (i=7;i>0;i=i-2)
{
if (substr(linedata[j],i,2) != "00") 
{printf "%" ;
 printf toupper(substr(linedata[j],i,2));
}
}
}
}' >"testurl.txt"
```
```bash
进行url解码
function urldecode(encypt_word)
{
        word=""
        for (i=1;i<=length(encypt_word);i++)
        {
                c=substr(encypt_word,i,1)
                if (c=="+") printf (" ");
                else if (c=="%") {
                        a=sprintf ("%c",hex[substr(encypt_word,++i,1)]*16+hex[substr(encypt_word,++i,1)])
                }
                else if (c=="\\" && substr(encypt_word, ++i, 1) == "x")
                {
                        a=sprintf ("%c",hex[substr(encypt_word,++i,1)]*16+hex[substr(encypt_word,++i,1)])
                }
                else
                        a=c
                word= word a
        }
        return word
}

BEGIN {
for (i=0;i<16;i++) hex[sprintf("%X",i)]=i;
for (i=0;i<16;i++) hex[sprintf("%x",i)]=i;
}

{
        bbb=urldecode($0)
        print bbb
}
```