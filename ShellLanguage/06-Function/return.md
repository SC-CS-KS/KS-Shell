# return

语法：
return [exit-value]

用途：
返回shell函数的退出值给调用它的脚本

行为模式：
如果未提供参数，则采用默认退出状态，也就是最后一个执行的命令退出时的状态。
如果这就是所需要的，那么严谨的shell函数写法是：
return $?
 
注意：
有些shell允许在脚本里使用return，但如果用于函数体外，等同于exit。
这种用法不建议，因为可能出现可移植性烦恼。
--------------------------------------------------------------------------------------------------
RETURN(1P)                 POSIX Programmer’s Manual                RETURN(1P)

PROLOG
       This  manual page is part of the POSIX Programmer’s Manual.  The Linux implementation of this interface may differ (consult the
       corresponding Linux manual page for details of Linux behavior), or the interface may not be implemented on Linux.

NAME
       return - return from a function

SYNOPSIS
       return [n]

DESCRIPTION
       The return utility shall cause the shell to stop executing the current function or dot script.    
       If the shell  is  not  currently executing a function or dot script, the results are unspecified.

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
       Not used.

STDERR
       The standard error shall be used only for diagnostic messages.

OUTPUT FILES
       None.

EXTENDED DESCRIPTION
       None.

EXIT STATUS
       The  value  of the special parameter ’?’ shall be set to n, an unsigned decimal integer, or to the exit status of the last com-
       mand executed if n is not specified. If the value of n is greater than 255, the results are undefined.  When return is executed
       in a trap action, the last command is considered to be the command that executed immediately preceding the trap action.

CONSEQUENCES OF ERRORS
       Default.

       The following sections are informative.

APPLICATION USAGE
       None.

EXAMPLES
       None.

RATIONALE
       The behavior of return when not in a function or dot script differs between the System V shell and the KornShell. In the System
       V shell this is an error, whereas in the KornShell, the effect is the same as exit.

       The results of returning a number greater than 255 are undefined because of  differing  practices  in  the  various  historical
       implementations. Some shells AND out all but the low-order 8 bits; others allow larger values, but not of unlimited size.

       See the discussion of appropriate exit status values under exit .

FUTURE DIRECTIONS
       None.

SEE ALSO
       Special Built-In Utilities

COPYRIGHT
       Portions of this text are reprinted and reproduced in electronic form from IEEE Std 1003.1, 2003 Edition, Standard for Informa-
       tion Technology -- Portable Operating System Interface (POSIX), The Open Group  Base  Specifications  Issue  6,  Copyright  (C)
       2001-2003  by  the  Institute  of Electrical and Electronics Engineers, Inc and The Open Group. In the event of any discrepancy
       between this version and the original IEEE and The Open Group Standard, the original IEEE and The Open Group  Standard  is  the
       referee document. The original Standard can be obtained online at http://www.opengroup.org/unix/online.html .

IEEE/The Open Group                  2003                           RETURN(1P)
--------------------------------------------------------------------------------------------------
