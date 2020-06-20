# od

od (octal dump)
命令可以以十进制、八进制、十六进制和ASCII码来显示文件或者流。
对于访问或可视地检查文件中不能直接显示在终端上的字符很有用。
-----------------------------------------------------------------------------------------------------------------------------
语法：od ［选项］ 文件… 
命令中各选项的含义：
-A 指定地址基数，包括：
d 十进制
o 八进制（系统默认值）
x 十六进制
n 不打印位移值

-t  指定数据的显示格式，主要的参数有：
c ASCII字符或反斜杠序列
d 有符号十进制数
f 浮点数
o 八进制（系统默认值为02）
u 无符号十进制数
x 十六进制数

除了选项c以外的其他选项后面都可以跟一个十进制数n，指定每个显示值所包含的字节数。
-----------------------------------------------------------------------------------------------------------------------------
说明：
od命令系统默认的显示方式是八进制，这也是该命令的名称由来（Octal Dump）。
但这不是最有用的显示方式，用ASCII码和十六进制组合的方式能提供更有价值的信息输出。
od -h 文件名
-----------------------------------------------------------------------------------------------------------------------------
NAME
       od - dump files in octal and other formats
SYNOPSIS
       od [OPTION]... [FILE]...
       od [-abcdfilosx]... [FILE] [[+]OFFSET[.][b]]
       od --traditional [OPTION]... [FILE] [[+]OFFSET[.][b] [+][LABEL][.][b]]
DESCRIPTION
       Write  an  unambiguous representation, octal bytes by default, of FILE to standard output.  With more than one FILE argu-
       ment, concatenate them in the listed order to form the input.  With no FILE, or when FILE is -, read standard input.

## od 实例

实例：
#cat testfile 
5678

# od -tx1 -tc -Ax testfile 
000000 35 36 37 38 0a
         5   6   7   8  \n
000005

-tx1   选项表示将文件中的字节以十六进制的形式列出来，每组一个字节
-tc    选项表示将文件中的ASCII码以字符形式列出来。输出结果最左边的一列是文件中的地址，默认以八进制显示。
-Ax    选项要求以十六进制显示文件中的地址。

