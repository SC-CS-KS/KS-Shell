# export

## export 使用

export用于修改或打印环境变量
主要用法是将变量放入环境里，环境是名称与值的简单列表，可供执行中的程序使用。新的进程会从父进程继承环境，也可以再建立新的子进程之前修改它。
主要选项：
-p 选项打印命令的名称，以及所有被导出变量的名称与值，这种方式可以使shell重新读取输出以重新建立环境。

注意：
最初的Bourne Shell要求，变量定义和导出分两步进行，而POSIX标准允许使用定义同时导出的方式。
但是许多商用系统里的/bin/sh 仍然不是POSIX兼容版本，要实现严格的可移植性，需使用以下方式到处变量：
var=sunny
export var
谨慎使用 export var=sunny
-------------------------------------------------------------------------------------------------------------------------
* export *   
严格来说，我们在当前shell中所定义的变量，均属于"本地变量"(local variable)。
只有经过export命令的"输出"处理，才能成为环境变量(environment variable)。
------------------------------------------------------------------------------------
代码:   
      $ A=B   
      $ export A   
或：
代码:   
      $ export A=B 

经过export输出处理之后，变量A就能成为一个环境变量供其后的命令使用。
------------------------------------------------------------------------------------
在使用 export的时侯，请别忘记shell在命令行对变量的"替换"(substitution)处理，比方说：
代码:   
      $ A=B   
      $ B=C   
      $ export $A   
上面的命令并未将A输出为环境变量，而是将B作输出。
这是因为在这个命令行中，$A会首先被提换出B然后再"塞回"作export的参数。
-------------------------------------------------------------------------------------------------------------------------

PROLOG
       This  manual  page is part of the POSIX Programmer's Manual.  The Linux implementation of this interface may differ (con-
       sult the corresponding Linux manual page for details of Linux behavior), or the  interface  may  not  be  implemented  on
       Linux.

NAME
       export - set the export attribute for variables

SYNOPSIS
       export name[=word]...

       export -p


DESCRIPTION
       The  shell  shall give the export attribute to the variables corresponding to the specified names, which shall cause them
       to be in the environment of subsequently executed commands. If the name of a variable is followed by  =  word,  then  the
       value of that variable shall be set to word.

       The export special built-in shall support the Base Definitions volume of IEEE Std 1003.1-2001, Section 12.2, Utility Syn-
       tax Guidelines.

       When -p is specified, export shall write to the standard output the names and values of all exported  variables,  in  the
       following format:
              "export %s=%s\n", <name>, <value>

       if name is set, and:
              "export %s\n", <name>

       if name is unset.

       The  shell  shall format the output, including the proper use of quoting, so that it is suitable for reinput to the shell
       as commands that achieve the same exporting results, except:
        1. Read-only variables with values cannot be reset.
        2. Variables that were unset at the time they were output need not be reset to the unset state if a value is assigned to
           the variable between the time the state was saved and the time at which the saved output is reinput to the shell.

       When no arguments are given, the results are unspecified.

OPTIONS
       See the DESCRIPTION.

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
       Zero.

CONSEQUENCES OF ERRORS
       Default.

       The following sections are informative.

APPLICATION USAGE
       None.

EXAMPLES
       Export PWD and HOME variables:


              export PWD HOME

       Set and export the PATH variable:


              export PATH=/local/bin:$PATH

       Save and restore all exported variables:


              export -p > temp-fileunset a lot of variables... processing. temp-file

RATIONALE
       Some  historical shells use the no-argument case as the functional equivalent of what is required here with -p. This fea-
       ture was left unspecified because it is not historical practice in all shells, and some scripts  may  rely  on  the  now-
       unspecified  results on their implementations. Attempts to specify the -p output as the default case were unsuccessful in
       achieving consensus. The -p option was added to allow portable access to the values that can  be  saved  and  then  later
       restored using; for example, a dot script.
