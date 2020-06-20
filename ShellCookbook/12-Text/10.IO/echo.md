# echo

## echo 用法

3)别人echo、你也echo，是问echo知多少？
*将argument送出至标准输出(STDOUT)，通常就是在监视器(monitor)上输出。

echo在预设上，在显示完  argument之后，还会送出一个换行符号(new-line charactor)。  
若你要取消这个换行符号，可利用echo的 -n option。

事实上，echo除了-n options之外，常用选项还有：
-e	：启用反斜线控制字符的转换(参考下表)   
-E         ：关闭反斜线控制字符的转换(预设如此)   
-n	：取消行末之换行符号(与 -e选项下的 \c字符同意)   

关于 echo命令所支援的反斜线控制字符如下表：
\a：ALERT / BELL (从系统喇叭送出铃声)   
\b：BACKSPACE，也就是向左删除键
\c：取消行末之换行符号
\E：ESCAPE，跳脱键
\f：FORMFEED，换页字符
\n：NEWLINE，换行字符
\r：RETURN，回车键
\t：TAB，表格跳位键
\v：VERTICAL TAB，垂直表格跳位键
\n：ASCII	八进位编码(以  x	开首为十六进位)   
\\：反斜线本身
(表格资料来自  O'Reilly	出版社之  Learning the Bash Shell, 2nd Ed.)   
------------------------------------------------------------------------
或许，我们可以透过实例来了解echo的选项及控制字符：
例一：
代码:   
$ echo -e "a\tb\tc\nd\te\tf"   
a              b              c   
d              e              f   

上例运用\t来区隔 abc还有def，及用 \n将  def换至下一行。
例二：
代码:   
$ echo -e "\141\011\142\011\143\012\144\011\145\011\146"   
a              b              c   
d              e              f   
与例一的结果一样，只是使用  ASCII	八进位编码。

例三：
代码:   
$ echo -e "\x61\x09\x62\x09\x63\x0a\x64\x09\x65\x09\x66"   
a              b              c   
d              e              f   
与例二差不多，只是这次换用 ASCII十六进位编码。  
-----------------------------------------------------------------------
用echo实现更复杂的输出格式控制：
显示转义字符
  echo "\"It is a test\""
  结果将是: "It is a test"。同样，双引号也可以省略

显示变量
  read name (输入OK)
  echo "$name It is a test"
  结果将是: OK It is a test。同样双引号也可以省略。

  如果变量与其它字符相连的话，需做如下处理:
  read mouth (输入8)
  echo "${mouth}-1-2009"
  结果将是: 8-1-2009

显示换行
  echo "OK!\n"
  echo "It it a test"

显示不换行
  echo "OK!\c"
  echo "It is a test"

显示结果定向至文件
  echo "It is a test" > myfile

原样输出字符串，不进行转义或取变量(用单引号)
  echo '$name\"'

显示命令执行结果
  echo `date`
  结果将显示当前日期

从上面可看出，双引号可有可无；单引号主要用在原样输出中。
-------------------------------------------------------------------------
实例：（旋转线）
#!/bin/ksh
interval=1
tcount="0"
while :
do
tcount=`expr $tcount + 1`
case $tcount in
"1")    echo -e '-'"\b\c"
sleep $interval
;;
"2")    echo -e '\\'"\b\c"
sleep $interval
;;
"3")    echo -e "|\b\c"
sleep $interval
;;
"4")    echo -e "/\b\c"
sleep $interval
;;
*)      tcount="0"
;;
esac
done

例子是《精通UNIX shell脚本编程》中的。
------------------------------------------------------------------------------------------------------------------------------------

## echo 打印颜色

shell脚本中echo显示内容带颜色显示,echo显示带颜色，需要使用参数-e 
格式如下： 

echo -e "\033[字背景颜色；文字颜色m字符串\033[0m" 
例如： 

echo -e "\033[41;36m something here \033[0m" 
其中41的位置代表底色， 36的位置是代表字的颜色 
注： 
　　1、字背景颜色和文字颜色之间是英文的"" 
　　2、文字颜色后面有个m 
　　3、字符串前后可以没有空格，如果有的话，输出也是同样有空格 
　　下面是相应的字和背景颜色，可以自己来尝试找出不同颜色搭配 
例 

echo -e “\033[31m 红色字 \033[0m” 
echo -e “\033[34m 黄色字 \033[0m” 
echo -e “\033[41;33m 红底黄字 \033[0m” 
echo -e “\033[41;37m 红底白字 \033[0m” 
字颜色：30—–37 

　　echo -e “\033[30m 黑色字 \033[0m” 
　　echo -e “\033[31m 红色字 \033[0m” 
　　echo -e “\033[32m 绿色字 \033[0m” 
　　echo -e “\033[33m 黄色字 \033[0m” 
　　echo -e “\033[34m 蓝色字 \033[0m” 
　　echo -e “\033[35m 紫色字 \033[0m” 
　　echo -e “\033[36m 天蓝字 \033[0m” 
　　echo -e “\033[37m 白色字 \033[0m” 

字背景颜色范围：40—–47 
　　echo -e “\033[40;37m 黑底白字 \033[0m” 
　　echo -e “\033[41;37m 红底白字 \033[0m” 
　　echo -e “\033[42;37m 绿底白字 \033[0m” 
　　echo -e “\033[43;37m 黄底白字 \033[0m” 
　　echo -e “\033[44;37m 蓝底白字 \033[0m” 
　　echo -e “\033[45;37m 紫底白字 \033[0m” 
　　echo -e “\033[46;37m 天蓝底白字 \033[0m” 
　　echo -e “\033[47;30m 白底黑字 \033[0m” 

最后面控制选项说明 
　　\33[0m 关闭所有属性 
　　\33[1m 设置高亮度 
　　\33[4m 下划线 
　　\33[5m 闪烁 
　　\33[7m 反显 
　　\33[8m 消隐 
　　\33[30m — \33[37m 设置前景色 
　　\33[40m — \33[47m 设置背景色 
　　\33[nA 光标上移n行 
　　\33[nB 光标下移n行 
　　\33[nC 光标右移n行 
　　\33[nD 光标左移n行 
　　\33[y;xH设置光标位置 
　　\33[2J 清屏 
　　\33[K 清除从光标到行尾的内容 
　　\33[s 保存光标位置 
　　\33[u 恢复光标位置 
　　\33[?25l 隐藏光标 
　　\33[?25h 显示光标



NAME
       echo - display a line of text

SYNOPSIS
       echo [OPTION]... [STRING]...

DESCRIPTION
       Echo the STRING(s) to standard output.

       -n     do not output the trailing newline

       -e     enable interpretation of backslash escapes

       -E     disable interpretation of backslash escapes (default)

       --help display this help and exit

       --version
              output version information and exit

       If -e is in effect, the following sequences are recognized:

       \0NNN  the character whose ASCII code is NNN (octal)

       \\     backslash

       \a     alert (BEL)

       \b     backspace

       \c     suppress trailing newline

       \f     form feed

       \n     new line

       \r     carriage return

       \t     horizontal tab

       \v     vertical tab

       NOTE:  your shell may have its own version of echo, which usually supersedes the version described here.  Please refer to
       your shell's documentation for details about the options it supports.
------------------------------------------------------------------------------------------------------------------------------------
