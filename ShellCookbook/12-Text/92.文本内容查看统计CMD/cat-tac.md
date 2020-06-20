# cat/tac

cat主要有三大功能：
01.一次显示整个文件。
   #cat   filename ~,y6;e
02.从键盘创建一个文件。 
   #cat  >  filename
   只能创建新文件，不能编辑已有文件。 :z)
03.将几个文件合并为一个文件。 
   #cat   file1   file2  > file"t
-------------------------------------------------------------------------------
参数： p:DS
-n 或 --number                                      由 1 开始对所有输出的行数编号
-b 或 --number-nonblank                       和 -n 相似，只不过对于空白行不编号
-s 或 --squeeze-blank                             当遇到有连续两行以上的空白行，就代换为一行的空白行
-v 或 --show-nonprinting                       显示非打印字符


## cat 实例

实例：
#cat -n textfile1 > textfile2 
把 textfile1 的档案内容加上行号后输入 textfile2 这个档案里 d

#cat -b textfile1 textfile2 >> textfile3 

#cat /dev/null > /etc/test.txt 
清空/etc/test.txt档案内容

## man page

NAME
       cat - concatenate(连结、使连锁) files and print on the standard output

SYNOPSIS
       cat [OPTION] [FILE]...

DESCRIPTION
       Concatenate FILE(s), or standard input, to standard output.
       -A, --show-all
              equivalent to -vET
       -b, --number-nonblank
              number nonempty output lines
       -e     equivalent to -vE
       -E, --show-ends
              display $ at end of each line
       -n, --number
              number all output lines
       -s, --squeeze-blank
              suppress repeated empty output lines
       -t     equivalent to -vT
       -T, --show-tabs
              display TAB characters as ^I
       -u     (ignored)
       -v, --show-nonprinting
              use ^ and M- notation, except for LFD and TAB
       --help display this help and exit
       --version
              output version information and exit

       With no FILE, or when FILE is -, read standard input.

EXAMPLES
       cat f - g
              Output f's contents, then standard input, then g's contents.

       cat    Copy standard input to standard output.
