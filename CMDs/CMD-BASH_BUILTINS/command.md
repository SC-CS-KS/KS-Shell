# command

## command 使用

command:
command [-pVv] command [arg ...]

Runs COMMAND with ARGS ignoring shell functions.  
If you have a shell function called `ls', and you wish to call the command `ls', you can say "command ls".  

If the -p option is given, a default value is used for PATH that is guaranteed to find all of the standard utilities.  
If the -V or -v option is given, a string is printed describing COMMAND. The -V option produces a more verbose description.

行为模式：
command会通过查找特殊和一般内建命令，以找出指定的程序，并沿着$PATH查找。
使用-p选项，是会使用$PATH的默认值，而非当前的设置。
如果程序为特殊内建命令，则任何的语法错误都不会退出Shell，并且任何的前置变量指定在命令完成后，即不再有效。

注意：
command非特殊内建命令，将函数命名为command的Shell程序设计人员，可能会很失望吧。



COMMAND(1P)                POSIX Programmer’s Manual               COMMAND(1P)

PROLOG
       This manual page is part of the POSIX Programmer’s Manual.  The Linux implementation of this interface may differ
       (consult the corresponding Linux manual page for details of Linux behavior), or the interface may not  be  imple-
       mented on Linux.

NAME
       command - execute a simple command

SYNOPSIS
       command [-p] command_name [argument ...]

       command [ -v | -V ] command_name