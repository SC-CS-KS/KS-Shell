# readonly

readonly 使用

readonly 使得变量不能被修改
主要选项：
-p 选项打印命令的名称，以及所有被只读变量的名称与值，这种方式可以使shell重新读取输出以重新建立只读设置。
注意：
许多商用系统里的/bin/sh 仍然不是POSIX兼容版本，要实现严格的可移植性，需使用以下方式到处变量：
var=sunny
readonly var
谨慎使用 readonly var=sunny
-------------------------------------------------------------------------------------------------------------------------


PROLOG
       This  manual  page is part of the POSIX Programmer's Manual.  The Linux implementation of this interface may differ (con-
       sult the corresponding Linux manual page for details of Linux behavior), or the  interface  may  not  be  implemented  on
       Linux.

NAME
       readonly - set the readonly attribute for variables

SYNOPSIS
       readonly name[=word]...

       readonly -p


DESCRIPTION
       The  variables whose names are specified shall be given the readonly attribute. The values of variables with the readonly
       attribute cannot be changed by subsequent(随后的) assignment, nor can those variables be unset by the unset utility. If the  name
       of a variable is followed by = word, then the value of that variable shall be set to word.

       The  readonly  special  built-in shall support the Base Definitions volume of IEEE Std 1003.1-2001, Section 12.2, Utility
       Syntax Guidelines.

       When -p is specified, readonly writes to the standard output the names and values of all read-only variables, in the fol-
       lowing format:


              "readonly %s=%s\n", <name>, <value>

       if name is set, and


              "readonly %s\n", <name>

       if name is unset.

       The  shell  shall format the output, including the proper use of quoting, so that it is suitable for reinput to the shell
       as commands that achieve the same value and readonly attribute-setting results in a shell execution environment in which:

        1. Variables with values at the time they were output do not have the readonly attribute set.

        2. Variables  that  were unset at the time they were output do not have a value at the time at which the saved output is
           reinput to the shell.

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
              readonly HOME PWD

RATIONALE
       Some historical shells preserve the readonly attribute across separate invocations. This volume  of  IEEE Std 1003.1-2001
       allows this behavior, but does not require it.

       The  -p  option  allows portable access to the values that can be saved and then later restored using, for example, a dot
       script.  Also see the RATIONALE for export for a description of the no-argument and -p output cases and a  related  exam-
       ple.

       Read-only  functions were considered, but they were omitted as not being historical practice or particularly useful. Fur-
       thermore, functions must not be read-only across invocations to preclude ``spoofing'' (spoofing is the term for the prac-
       tice of creating a program that acts like a well-known utility with the intent of subverting the real intent of the user)
       of administrative or security-relevant (or security-conscious) shell scripts.

FUTURE DIRECTIONS
       None.

SEE ALSO
       Special Built-In Utilities
-------------------------------------------------------------------------------------------------------------------------
