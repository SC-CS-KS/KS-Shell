# tee

从标准输入中读取并同时写入到标准输出和指定的文件上

总揽：
    tee [Options]…… [File]……

选项：
    -a,--append:不覆盖，而是追加输出到指定的文件中
    -i,--ignore-interrupts:忽略中断信息
    --help:显示帮助信息并退出
    --version:显示版本信息并退出
    若指定的输出文件为'-'，则再次输出到标准输出上

## tree 实例

实例：
    tee Example.txt 
    #将标准输入中输入的内容同时输出到Example.txt和标准输出上

    tee -a Example.txt  
    #追加模式输出到Example.txt

    cat Example.txt |tee e2.txt 
    #将cat的输出输入到标准输出和e2.txt文件中

## man tee

NAME
       tee - read from standard input and write to standard output and files

SYNOPSIS
       tee [OPTION]... [FILE]...

DESCRIPTION
       Copy standard input to each FILE, and also to standard output.

       -a, --append
              append to the given FILEs, do not overwrite

       -i, --ignore-interrupts
              ignore interrupt signals

       --help display this help and exit

       --version
              output version information and exit

       If a FILE is -, copy again to standard output.

AUTHOR
       Written by Mike Parker, Richard M. Stallman, and David MacKenzie.

REPORTING BUGS
       Report bugs to <bug-coreutils@gnu.org>.

COPYRIGHT
       Copyright   (C)   2008   Free   Software   Foundation,   Inc.    License   GPLv3+:   GNU   GPL   version   3   or   later
       <http://gnu.org/licenses/gpl.html>
       This is free software: you are free to change and redistribute it.  There is NO WARRANTY, to the extent permitted by law.

SEE ALSO
       The full documentation for tee is maintained as a Texinfo manual.  If the info and tee programs are properly installed at
       your site, the command

              info coreutils 'tee invocation'

       should give you access to the complete manual.
-------------------------------------------------------------------

