# printf

printf命令：
格式化输出命令，是echo命令的增强版。它是C语言printf()库函数的一个有限的变形，并且在语法上有些不同。
注意：
使用printf的脚本比使用echo移植性好。
---------------------------------------------------------------------------------------------------
printf命令的完整语法有两个部分：
printf的语法格式：printf format-string [arguments...]
第一部分为描述格式规格的字符串，用来描述输出的排列方式，最好为此字符串加上引号。
此字符串包含按字面显示的字符以及格式声明，格式声明时特殊的占位符，用来描述如何显示相应的参数。

第二部分是与格式声明相对应的参数列表，例如一系列的字符串或变量值。
格式声明由两部分组成：
百分比符号（%）和指示符。最常用的格式指示符有两个，%s用于字符串，而%d用于十进制整数。

格式字符串中，一般字符会按字面显示。转义序列则像echo那样，解释后再输出成相应的字符。
格式声明以%符号开头，并以定义的字母集中的一个来结束，用来控制相应参数的输出。

例如%s用于字符串的输出：
    # printf "The first program always prints'%s,%d\n'" Hello Shell
输出结果为：
    -bash: printf: Shell: invalid number
    The first program always prints 'Hello,0

当尝试以%d的格式来显示字符串Shell时，会发现有警告，提示Shell为无效的数字，此时会打印出默认值0。
从这个试验中可以看出来：%s, %d 两侧的单引号''并不是必须的。
---------------------------------------------------------------------------------------------------
printf的转义序列：
\a   警告字符，通常为ASCII的BEL字符
\b   后退
\c   抑制（不显示）输出结果中任何结尾的换行字符（只在%b格式指示符控制下的参数字符串中有效）。
     而且，任何留在参数里的字符、任何接下来的参数以及任何留在格式字符串中的字符，都被忽略
\f   换页（formfeed）
\n   换行
\r   回车（Carriage return）
\t   水平制表符
\v   垂直制表符
\\   一个字面上的反斜杠字符
\ddd 表示1到3位数八进制值的字符。仅在格式字符串中有效。
\0ddd 表示1到3位的八进制值字符

默认情况下，转义序列只在格式字符串中会被特别对待，也就是说，如果转义序列出现在参数列表的字符串中，将不会被解释：
#printf "a string, no processing:<%s>\n" "A\nB"

当你使用%b格式指示符时，printf会解释参数字符串里的转义序列：
#printf "a string, no processing:<%b>\n" "A\nB"
注："A\nB"中的\n会被解释为换行符

无论时在格式字符串内还是在使用%b所打印的参数字符串里，大部分的转义序列都是被相同对待。
无论如何，\c与\0ddd只有搭配%b使用才有效，而\ddd只有在格式字符串里才会被解释。
---------------------------------------------------------------------------------------------------
printf格式指示符
%b         相对应的参数被视为含有要被处理的转义序列之字符串。
%c         ASCII字符。显示相对应参数的第一个字符
%d, %i     十进制整数
%e         浮点格式
%E         浮点格式
%f         浮点格式
%g         %e或%f转换，看哪一个较短，则删除结尾的零
%G         %E或%f转换，看哪一个较短，则删除结尾的零
%o         不带正负号的八进制值
%s         字符串
%u         不带正负号的十进制值
%x         不带正负号的十六进制值，使用a至f表示10至15
%X         不带正负号的十六进制值，使用A至F表示10至15
%%         字面意义的%

根据POSIX标准：
浮点格式%e、%E、%f、%g与%G是“不需要被支持”。这是因为awk支持浮点预算，且有它自己的printf语句。
这样Shell程序中需要将浮点数值进行格式化的打印时，可使用小型的awk程序实现。
然而，内建于bash、ksh93和zsh中的printf命令都支持浮点格式。
---------------------------------------------------------------------------------------------------
%flags width.precision format-specifier
默认输出左对齐
width用于指定字段宽度，当指定字段宽度时，字段默认右对齐，如果要左对齐，必须指定-符号。

precision数值精度的意义：
%d,%i,%o,%u,%x,%X   要打印的最小位数。当值的位数小于此数字时，会在前面补零。默认精度（precision）为1
%e，%E              要打印的最小位数。当值的位数小于此数字时，会在小数点后面补零，默认精度为6。精度为0时则表示不显示小数点
%f                  小数点右边的位数
%g，%G              有效位数（significant digit）的最大数目
%s                  要打印字符的最大数目

#printf "%f\n"  3.25          
3.250000
#printf "%10f\n"  3.25 
  3.250000
#printf "%-10f\n"  3.25
3.250000  
#printf "%10.1f\n"  3.25 
       3.2
#printf "%-10.1f\n"  3.25
3.2    
---------------------------------------------------------------------------------------------------
C语言里printf函数中可以通过参数列表，动态指定字段宽度和精度值。
POSIX不支持该功能，它建议在格式字符串中使用shell变量值。

#width=10 prec=3 my_var=3.52333          
#printf "%-${width}.${prec}f\n"  $my_var
3.523     
---------------------------------------------------------------------------------------------------
可以在字段宽度和精度之前放置一个或多个标志，如 -
printf的flags：
-      表示左对齐
空格   在正值前置一个空格，负值前置负号
+      在值前置正负号
#      %o            前置一个0
       %x %X         前置0x、0X
       %e %E %f      结果中总有一个小数点
       %g %G         为没有结尾的0
0      以0填补空白，而不是空格。在C语言中，它可以应用到所有的输出类型。Shell中，它只应用于数值格式。

例：
#printf "% d  % d\n" 15  -15  
 15  -15
#printf "%+d  %+d\n" 15  -15  
+15  -15
#printf "%#o\n" 15
017
#printf "%#X\n" 15 
0XF
#printf "%#x\n" 15 
0xf
#printf "%05X\n" 15 
0000F
---------------------------------------------------------------------------------------------------
对于%b %s %c，对应参数全部解释为字符串。
否则会被解释为C语言的数字常数(0开头八进制、0X和0x开头为十六进制)
#printf "%d\n" 0x11
17
#printf "%d\n" 011 
9

如果参数的第一个字符为单引号或双引号，则相对应的数值是字符串第二个字符的ASCII值：
#printf "%c is %d\n" a "'a"
a is 97
---------------------------------------------------------------------------------------------------
当参数值个数多于格式化指示符时，会循环用格式字符串匹配：
#printf "%c%c\n" a b c
ab
c

如果是参数值少于格式化指示符：
如果是数值转换，则遗漏的值会被看成0
#printf "%d%d\n" 8  
80
如果是字符串转换，则视为空字符串。
---------------------------------------------------------------------------------------------------
如果printf无法进行格式的转换，返回一个非零的退出状态。
---------------------------------------------------------------------------------------------------


## help printf

printf: 
printf [-v var] format [arguments]
 
printf formats and prints ARGUMENTS under control of the FORMAT. 
FORMAT is a character string which contains three types of objects: plain characters, which are simply copied to standard output, character escape sequences which are converted and copied to the standard output, and format specifications, each of which causes printing of the next successive argument.  

In addition to the standard printf(1) formats, %b means to expand backslash escape sequences in the corresponding argument, and %q means to quote the argument in a way that can be reused as shell input.
    
If the -v option is supplied, the output is placed into the value of the shell variable VAR rather than being sent to the standard output.
#printf -v var "%f"  3.25  
#echo $var               
3.250000

#/usr/bin/printf -v var "%f" 3.5  
-v/usr/bin/printf: warning: ignoring excess arguments, starting with `var'
//外部命令printf没有-v选项
---------------------------------------------------------------------------------------------------------------------------------------------


## man 1 printf

/usr/bin/printf
NAME
       printf - format and print data
SYNOPSIS
       printf FORMAT [ARGUMENT]...
       printf OPTION
DESCRIPTION
       Print ARGUMENT(s) according to FORMAT, or execute according to OPTION:
       --help display this help and exit
       --version
              output version information and exit
       FORMAT controls the output as in C printf.  Interpreted sequences are:
       \"     double quote
       \NNN   character with octal value NNN (1 to 3 digits)
       \\     backslash
       \a     alert (BEL)
       \b     backspace
       \c     produce no further output
       \f     form feed
       \n     new line
       \r     carriage return
       \t     horizontal tab
       \v     vertical tab
       \xHH   byte with hexadecimal value HH (1 to 2 digits)
       \uHHHH Unicode (ISO/IEC 10646) character with hex value HHHH (4 digits)
       \UHHHHHHHH
              Unicode character with hex value HHHHHHHH (8 digits)
       %%     a single %
       %b     ARGUMENT as a string with `\' escapes interpreted, except that octal escapes are of the form \0 or \0NNN

       and  all  C format specifications ending with one of diouxXfeEgGcs, with ARGUMENTs converted to proper type first.  Vari-
       able widths are handled.

       NOTE: your shell may have its own version of printf, which usually supersedes the version described here.   Please  refer
       to your shell's documentation for details about the options it supports.
-----------------------------------------------------------------------------------------------------------------------------------------
