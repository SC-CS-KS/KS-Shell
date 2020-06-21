# 20.Shell Exit Status

每一条命令，不管是内置的、shell函数，还是外部的，当它退出是，都会返回一个小的整数给引用它的程序。
这就是程序退出状态(exit status)

POSIX的退出状态
0        命令成功退出
> 0      在重定向或单词展开(~、变量、命令、算术展开，以及单词切割)是失败，
1-125    命令不成功退出，特定的退出含义，由命令自己定义。
126      命令找到，但文件无法执行，
127      命令找不到，
> 128    命令因为收到信号而死亡。

128未POSIX定义，仅要求表示某种失败。
只有低位的8个位会返回给父进程，超过255的退出状态，都会返回除以256之后的余数。


## exit 命令

exit命令
exit命令用于退出当前shell，在shell脚本中可以终止当前脚本执行。

常用参数
格式：exit n
（Cause the shell to exit with a status of n.）
在shell脚本中增加自定义的退出码是一个好的习惯。
 
格式：exit
（If n is omitted, the exit status is that of the  last  command executed.）
 
格式：trap "commands" EXIT
退出时执行commands指定的命令。（ A trap on EXIT is executed before the shell terminates.）

exit帮助(man 3 exit)有如下叙述：
The C standard specifies two constants, EXIT_SUCCESS and EXIT_FAILURE, that may be passed to exit() to indicate
successful or unsuccessful termination, respectively.
 
以下摘自/usr/include/stdlib.h
C代码  
#define EXIT_FAILURE    1       /* Failing exit status.  */  
#define EXIT_SUCCESS    0       /* Successful exit status.  */  
 
BSD试图对退出码标准化
man 3 exit 写道
BSD has attempted to standardize exit codes; see the file <sysexits.h>.
 
以下摘自/usr/include/sysexits.h
C代码  
#define EX_OK           0       /* successful termination */    
#define EX__BASE        64      /* base value for error messages */  
#define EX_USAGE        64      /* command line usage error */  
#define EX_DATAERR      65      /* data format error */  
#define EX_NOINPUT      66      /* cannot open input */  
#define EX_NOUSER       67      /* addressee unknown */  
#define EX_NOHOST       68      /* host name unknown */  
#define EX_UNAVAILABLE  69      /* service unavailable */  
#define EX_SOFTWARE     70      /* internal software error */  
#define EX_OSERR        71      /* system error (e.g., can't fork) */  
#define EX_OSFILE       72      /* critical OS file missing */  
#define EX_CANTCREAT    73      /* can't create (user) output file */  
#define EX_IOERR        74      /* input/output error */  
#define EX_TEMPFAIL     75      /* temp failure; user is invited to retry */  
#define EX_PROTOCOL     76      /* remote error in protocol */  
#define EX_NOPERM       77      /* permission denied */  
#define EX_CONFIG       78      /* configuration error */    
#define EX__MAX         78      /* maximum listed value */  

实例
退出当前shell
# exit 
logout
 
在脚本中，进入脚本所在目录，否则退出
Bash代码  
cd $(dirname $0) || exit 1  
 
在脚本中，判断参数数量，不匹配就打印使用方式，退出
Bash代码  
if [ "$#" -ne "2" ]; then                             #test命令的-ne选项表示整数判断中的不等于
   echo "usage: $0 <area> <hours>"  
   exit 2  
fi  
 
在脚本中，退出时删除临时文件
Bash代码  
trap "rm -f tmpfile; echo Bye." EXIT                 #表示trap收到EXIT信号时执行rm命令
 
检查上一命令的退出码
Bash代码  ./mycommand.sh  
EXCODE=$?  
if [ "$EXCODE" == "0" ]; then  
  echo "O.K"  
fi  

## man page
PROLOG
       This  manual  page is part of the POSIX Programmer's Manual.  The Linux implementation of this interface may differ (con-
       sult the corresponding Linux manual page for details of Linux behavior), or the  interface  may  not  be  implemented  on
       Linux.
NAME
       exit - cause the shell to exit
SYNOPSIS
       exit [n]
DESCRIPTION
       The exit utility shall cause the shell to exit with the exit status specified by the unsigned decimal integer n.  If n is
       specified, but its value is not between 0 and 255 inclusively, the exit status is undefined.
       A trap on EXIT shall be executed before the shell terminates, except when the  exit  utility  is  invoked  in  that  trap
       itself, in which case the shell shall exit immediately.

