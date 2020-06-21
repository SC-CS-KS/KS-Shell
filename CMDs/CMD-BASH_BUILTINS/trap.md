# trap

## trap 使用

用途说明
trap是一个shell内建命令，它用来在脚本中指定信号如何处理。
比如，按Ctrl+C会使脚本终止执行，实际上系统发送了SIGINT信号给脚本进程，SIGINT信号的默认处理方式就是退出程序。
如果要在Ctrl+C不退出程序，那么就得使用trap命令来指定一下SIGINT的处理方式了。
trap命令还能对脚本退出（EXIT）、调试（DEBUG）、错误（ERR）、返回（RETURN）等情况指定处理方式。
--------------------------------------------------------------------------------------------------------------------
常用用法
trap [-lp] [[arg] sigspec ...]
--------------------------------------------------------------------------------------
格式：trap "commands" signals
当shell接收到signals指定的信号时，执行commands命令。
（The  command arg is to be read and executed when the shell receives signal(s) sigspec. ）
--------------------------------------------------------------------------------------
格式：trap signals
如果没有指定命令部分，那么就将信号处理复原。比如 trap INT 就表明恢复Ctrl+C退出。
（If arg is absent (and there is a single sigspec) or -, each specified signal is reset to its  original  disposition  (the value  it  had  upon  entrance  to  the  shell). ）
--------------------------------------------------------------------------------------
格式：trap "" signals
忽略信号signals，可以多个。
比如 trap "" INT 表明忽略SIGINT信号，按Ctrl+C也不能使脚本退出。又如 trap "" HUP 表明忽略SIGHUP信号，即网络断开时也不能使脚本退出。
（If arg is the null string the signal specified by each sigspec is ignored by the shell and by the commands it invokes. ）
--------------------------------------------------------------------------------------
格式：trap -p
格式：trap -p signal
把当前的trap设置打印出来。
（If arg is not present and -p  has  been supplied,  then  the trap commands associated with each sigspec are displayed.  If no arguments are supplied or if only -p is given, trap prints the list of commands associated  with  each  signal.）
-------------------------------------------------------------------------------------- 
格式：trap -l
把所有信号打印出来。
（The  -l option  causes  the shell to print a list of signal names and their corresponding numbers.  Each sigspec is either a signal name defined in <signal.h>, or a signal number.  Signal names  are  case  insensitive and  the  SIG prefix is optional.） 
-------------------------------------------------------------------------------------- 
格式：trap "commands" EXIT
脚本退出时执行commands指定的命令。
（If a sigspec is EXIT (0) the command arg is executed on exit from the shell.）
--------------------------------------------------------------------------------------
格式：trap "commands" DEBUG
在脚本执行时打印调试信息，比如打印将要执行的命令及参数列表。
（If a sigspec is DEBUG, the command arg is executed before every  simple  command,  for  command, case  command,  select command, every arithmetic for command, and before the first command executes in a shell function (see SHELL GRAMMAR above).  Refer to the description of the extdebug option to the  shopt builtin  for  details of its effect on the DEBUG trap.）
-------------------------------------------------------------------------------------- 
格式：trap "commands" ERR
当命令出错，退出码非0，执行commands指定的命令。（If a sigspec is ERR, the command arg is executed whenever a simple command has a non-zero exit status, subject to the following conditions.  The ERR trap is not executed if the failed command is part of the command list immediately following a while or until keyword, part of the test in an if statement, part of a && or ┅Ι│ list, or if the command’s return  value is  being  inverted via !.  These are the same conditions obeyed by the errexit option.）
--------------------------------------------------------------------------------------
格式：trap "commands" RETURN
当从shell函数返回、或者使用source命令执行另一个脚本文件时，执行commands指定的命令。（If a sigspec is RETURN, the command arg is executed each time a shell function or a script executed with the . or source    builtins  finishes  executing.   Signals  ignored  upon  entry  to the shell cannot be trapped or reset. Trapped signals that are not being ignored are reset to their original values in a child process when it is created.  The return status is false if any sigspec is invalid; otherwise trap returns true.）
--------------------------------------------------------------------------------------------------------------------------------------------------
trap命令用于指定在接收到信号后将要采取的行动。trap命令的一种常见用途是在脚本程序被中断时完成清理工作。
历史上，shell总是用数字来代表信号，而新的脚本程序应该使用信号的名字，它们保存在用#include命令包含进来的signal.h头文件中。
在使用信号名时需要省略SIG前缀。你可以在命令提示符下输入命令trap -l来查看信号编号及其关联的名称。

trap命令的参数分为两部分：
前一部分是接收到指定信号时将要采取的行动，后一部分是要处理的信号名。
请记住，脚本程序通常是以从上到下的顺序解释执行的，所以必须在你想保护的那部分代码以前指定trap命令。
 
表列出了X/Open规范里面规定的能够被捕获的比较重要的一些信号（括号里面的数字是传统的信号编号）。
信     号
说     明
HUP(1)
挂起，通常因终端掉线或用户退出而引发
INT(2)
中断，通常因按下Ctrl+C组合键而引发
QUIT(3)
退出，通常因按下Ctrl+\组合键而引发
ABRT(6)
中止，通常因某些严重的执行错误而引发
ALRM(14)
报警，通常用来处理超时
TERM(15)
终止，通常在系统关机时发送
--------------------------------------------------------------------------------------------------------------------------------------------------
trap捕捉到信号之后，可以有三种反应方式：
执行一段程序来处理这一信号
接受信号的默认操作
忽视这一信号

trap对上面三种方式提供了三种基本形式：
第一种形式的trap命令在shell接收到signal list清单中数值相同的信号时，将执行双引号中的命令串：
trap 'commands' signal-list
trap "commands" signal-list
第二种形式的trap命令，为了恢复信号的默认操作：
trap signal-list
第三种形式的trap命令允许忽视信号：
trap " " signal-list
--------------------------------------------------------------------------------------
在第一种形式中，执行命令，对于双引号和单引号是有区别的。
#/bin/bash
#
#双引号，shell第一次设置信号的时候就执行命令和变量的替换，时间不变t，意即date只执行一次。
trap "echo `date`:can not terminate by ctrl+C" 2
#
#单引号，要在shell探测到信号来的时候才执行命令和变量的替换，时间一直变t，意即trap执行一次date执行一次。
trap 'echo `date`:can not terminate by ctrl+C' 2

while [ 1 ]   
do              
    echo -n "input a num : "
    read num 
    if [ $num -eq -1 ]
    then
        echo "bye"
        break
     fi
     echo "you have enter $num"
done

注：
[  ] 返回false
[ 1 ]  [ !1 ] [ 0 ] [ !0 ] 都返回 true
 #test 可以写成 while :  或 while ((1))
--------------------------------------------------------------------------------------------------------------------------------------------------


trap [-lp] [arg signal_spec ...]

The command ARG is to be read and executed when the shell receives signal(s) SIGNAL_SPEC.  
-------------------------------------------------------------------------------------------------------------------------------
If ARG is absent (and a single SIGNAL_SPEC is supplied) or `-', each specified signal is reset to its original value.  
If ARG is the null string each SIGNAL_SPEC is ignored by the shell and by the commands it invokes.  
If a SIGNAL_SPEC is EXIT (0) the command ARG is executed on exit from the shell.  
If a SIGNAL_SPEC is DEBUG, ARG is executed after every simple command.  

If the`-p' option is supplied then the trap commands associated with each SIGNAL_SPEC are displayed.  
If no arguments are supplied or if only `-p' is given, trap prints the list of commands associated with each signal.  

Each SIGNAL_SPEC is either a signal name in <signal.h> or a signal number. 
Signal names are case insensitive and the SIG prefix is optional.  

`trap -l' prints a list of signal names and their corresponding numbers.  
等价于 kill -l

Note that a signal can be sent to the shell with "kill -signal $$".
-------------------------------------------------------------------------------------------------------------------------------
PROLOG
       This  manual  page is part of the POSIX Programmer's Manual.  The Linux implementation of this interface may differ (con-
       sult the corresponding Linux manual page for details of Linux behavior), or the  interface  may  not  be  implemented  on
       Linux.

NAME
       trap - trap signals

SYNOPSIS
       trap [action condition ...]

DESCRIPTION
       If  action  is  '-', the shell shall reset each condition to the default value. 
       If  action is null ( "" ), the shell shallignore each specified condition if it arises.
       Otherwise, the argument action shall be read and executed by the shell when one of the corresponding conditions arises. 
       The action of trap shall override a previous action (either default action orone explicitly set). 
       The value of "$?" after the trap action completes shall be the value it had before trap was invoked.

       The  condition  can be EXIT, 0 (equivalent to EXIT), or a signal specified using a symbolic name, without the SIG prefix,
       as listed in the  tables  of  signal  names  in  the  <signal.h>  header  defined  in  the  Base  Definitions  volume  of
       IEEE Std 1003.1-2001,  Chapter  13, Headers; for example, HUP, INT, QUIT, TERM. Implementations may permit names with the
       SIG prefix or ignore case in signal names as an extension. Setting a trap  for  SIGKILL  or  SIGSTOP  produces  undefined
       results.

       The  environment  in  which the shell executes a trap on EXIT shall be identical to the environment immediately after the
       last command executed before the trap on EXIT was taken.

       Each time trap is invoked, the action argument shall be processed in a manner equivalent to:


              eval action

       Signals that were ignored on entry to a non-interactive shell cannot be trapped or  reset,  although  no  error  need  be
       reported  when  attempting to do so. An interactive shell may reset or catch signals ignored on entry. Traps shall remain
       in place for a given shell until explicitly changed with another trap command.

       When a subshell is entered, traps that are not being ignored are set to the default actions. This does not imply that the
       trap command cannot be used within the subshell to set new traps.

       The  trap command with no arguments shall write to standard output a list of commands associated with each condition. The
       format shall be:


              "trap -- %s %s ...\n", <action>, <condition> ...

       The shell shall format the output, including the proper use of quoting, so that it is suitable for reinput to  the  shell
       as commands that achieve the same trapping results. For example:


              save_traps=$(trap)
              ...
              eval "$save_traps"

       XSI-conformant systems also allow numeric signal numbers for the conditions corresponding to the following signal names:

                                                      Signal Number   Signal Name
                                                      1               SIGHUP
                                                      2               SIGINT
                                                      3               SIGQUIT
                                                      6               SIGABRT
                                                      9               SIGKILL
                                                      14              SIGALRM

                                                      15              SIGTERM

       The  trap  special  built-in  shall conform to the Base Definitions volume of IEEE Std 1003.1-2001, Section 12.2, Utility
       Syntax Guidelines.

OPTIONS
       None.

OPERANDS
       See the DESCRIPTION.

STDIN
       Not used.

INPUT FILES
       None.

ENVIRONMENT VARIABLES
       None.

ASYNCHRONOUS EVENTS
       Default.

STDOUT
       See the DESCRIPTION.

STDERR
       The standard error shall be used only for diagnostic messages.

OUTPUT FILES
       None.

EXTENDED DESCRIPTION
       None.

EXIT STATUS
       If the trap name  or number  is invalid, a non-zero exit status shall be returned; otherwise,  zero  shall  be  returned.
       For both interactive and non-interactive shells, invalid signal names  or numbers  shall not be considered a syntax error
       and do not cause the shell to abort.

CONSEQUENCES OF ERRORS
       Default.

       The following sections are informative.

APPLICATION USAGE
       None.

EXAMPLES
       Write out a list of all traps and actions:


              trap

       Set a trap so the logout utility in the directory referred to by the HOME environment variable executes  when  the  shell
       terminates:


              trap '$HOME/logout' EXIT

       or:


              trap '$HOME/logout' 0

       Unset traps on INT, QUIT, TERM, and EXIT:


              trap - INT QUIT TERM EXIT

RATIONALE(基本原理)
       Implementations  may  permit  lowercase signal names as an extension.  Implementations may also accept the names with the
       SIG prefix; no known historical shell does so. The trap and kill utilities in this volume of IEEE Std 1003.1-2001 are now
       consistent  in  their omission of the SIG prefix for signal names. Some kill implementations do not allow the prefix, and
       kill -l lists the signals without prefixes.

       Trapping SIGKILL or SIGSTOP is syntactically accepted by some historical implementations, but it has no effect.  Portable
       POSIX applications cannot attempt to trap these signals.

       The  output  format  is  not  historical  practice. Since the output of historical trap commands is not portable (because
       numeric signal values are not portable) and had to change to become so, an opportunity was taken to format the output  in
       a way that a shell script could use to save and then later reuse a trap if it wanted.

       The  KornShell uses an ERR trap that is triggered whenever set -e would cause an exit. This is allowable as an extension,
       but was not mandated, as other shells have not used it.

       The text about the environment for the EXIT trap invalidates the behavior of  some  historical  versions  of  interactive
       shells  which, for example, close the standard input before executing a trap on 0. For example, in some historical inter-
       active shell sessions the following trap on 0 would always print "--" :


              trap 'read foo; echo "-$foo-"' 0
