# read/read array

read是一个shell内建命令
语法
read [ -p ][  -r ][ -s ][ -u[ n ] ] [ VariableName ... ]
------------------------------------------------------------------------------------------------------------
描述
read 命令从标准输入中读取一行，并把输入行的每个字段的值指定给 shell 变量，用IFS（内部字段分隔符）变量中的字符作为分隔符。

如果输入行以反斜杠结束，则read会丢弃反斜杠和换行符，然后继续读取下一行数据（这里的反斜杠相当于行的连接符，表示输入没有完成）。如果使用了-r选项，那么read会以字面意思读取最后的反斜杠，最后的反斜杠会被保存到变量中。

VariableName 参数指定 shell 变量的名称，shell 变量获取输入行一个字段的值。
如果标准输入行的字段比相应的由 VariableName 参数指定的 shell 变量的个数多，把全部余下的字段的值赋给指定的最后的 shell 变量。
如果比 shell 变量的个数少，则剩余的 shell 变量被设置为空字符串。
------------------------------------------------------------------------------------------------------------
注意：
01.如果省略了 VariableName 参数，变量 REPLY 用作缺省变量名。
02.当read应用在管道时，许多shell会在一个分开的进程里执行。
   在这种情况下，任何以read所设置的变量，都不会都不会保留他们在父shell里的值。对管道中间的循环也是这样。
   即由 read 命令设置的 shell 变量影响当前 shell 执行环境。
------------------------------------------------------------------------------------------------------------
选项：
-r  指定读取命令把一个 \ (反斜杠) 处理为输入行的一部分，而不把它作为一个控制字符。
-p  用 |& （管道，& 的记号名称）读取由 Korn shell 运行的进程的输出作为输入。
    注：-p 标志的文件结束符引起该进程的清除，因此产生另外一个进程。
-s  把输入作为一个命令保存在 Korn shell 的历史记录文件中。
-u  [ n ] 读取一位数的文件描述符号码 n 作为输入。
    文件描述符可以用 ksh exec 内置命令打开。n 的缺省值是 0，表示的是键盘。值 2 表示标准错误。
------------------------------------------------------------------------------------------------------------
退出状态
0 成功完成，>0 检测到文件结束符或一个错误发生。
------------------------------------------------------------------------------------------------------------
$ help read
read: read [-ers] [-a array] [-d delim] [-i text] [-n nchars] [-N nchars] [-p prompt] [-t timeout] [-u fd] [name ...]
    Read a line from the standard input and split it into fields.
    
    Reads a single line from the standard input, or from file descriptor FD
    if the -u option is supplied.  The line is split into fields as with word
    splitting, and the first word is assigned to the first NAME, the second
    word to the second NAME, and so on, with any leftover words assigned to
    the last NAME.  Only the characters found in $IFS are recognized as word
    delimiters.
    
    If no NAMEs are supplied, the line read is stored in the REPLY variable.
    
    Options:
      -a array  assign the words read to sequential indices of the array
                variable ARRAY, starting at zero
      -d delim  continue until the first character of DELIM is read, rather
                than newline
      -e                use Readline to obtain the line in an interactive shell
      -i text   Use TEXT as the initial text for Readline
      -n nchars return after reading NCHARS characters rather than waiting
                for a newline, but honor a delimiter if fewer than NCHARS
                characters are read before the delimiter
      -N nchars return only after reading exactly NCHARS characters, unless
                EOF is encountered or read times out, ignoring any delimiter
      -p prompt output the string PROMPT without a trailing newline before
                attempting to read
      -r                do not allow backslashes to escape any characters
      -s                do not echo input coming from a terminal
      -t timeout        time out and return failure if a complete line of input is
                not read withint TIMEOUT seconds.  The value of the TMOUT
                variable is the default timeout.  TIMEOUT may be a
                fractional number.  If TIMEOUT is 0, read returns success only
                if input is available on the specified file descriptor.  The
                exit status is greater than 128 if the timeout is exceeded
      -u fd             read from file descriptor FD instead of the standard input
    
    Exit Status:
    The return code is zero, unless end-of-file is encountered, read times out,
    or an invalid file descriptor is supplied as the argument to -u.

readarray: readarray [-n count] [-O origin] [-s count] [-t] [-u fd] [-C callback] [-c quantum] [array]
    Read lines from a file into an array variable.
------------------------------------------------------------------------------------------------------------
READ(1P)                   POSIX Programmer’s Manual                  READ(1P)

PROLOG
       This  manual page is part of the POSIX Programmer’s Manual.  The Linux implementation of this interface may differ (consult the
       corresponding Linux manual page for details of Linux behavior), or the interface may not be implemented on Linux.

NAME
       read - read a line from standard input

SYNOPSIS
       read [-r] var...

DESCRIPTION
       The read utility shall read a single line from standard input.

       By default, unless the -r option is specified, backslash ( ’\’ ) shall act as an escape character, as described in Escape Char-
       acter  (Backslash) . If standard input is a terminal device and the invoking shell is interactive, read shall prompt for a con-
       tinuation line when:

        * The shell reads an input line ending with a backslash, unless the -r option is specified.

        * A here-document is not terminated after a <newline> is entered.

       The line shall be split into fields as in the shell (see Field Splitting ); the first field shall  be  assigned  to  the  first
       variable var, the second field to the second variable var, and so on.  If there are fewer var operands specified than there are
       fields, the leftover fields and their intervening separators shall be assigned to the last var. If there are fewer fields  than
       vars, the remaining vars shall be set to empty strings.

       The  setting  of variables specified by the var operands shall affect the current shell execution environment; see Shell Execu-
       tion Environment . If it is called in a subshell or separate utility execution environment, such as one of the following:

              (read foo)
              nohup read ...
              find . -exec read ... \;

       it shall not affect the shell variables in the caller’s environment.

OPTIONS
       The read utility shall conform to the Base Definitions volume of IEEE Std 1003.1-2001, Section 12.2, Utility Syntax Guidelines.

       The following option is supported:

       -r     Do not treat a backslash character in any special way. Consider each backslash to be part of the input line.

OPERANDS
       The following operand shall be supported:

       var    The name of an existing or nonexisting shell variable.

STDIN
       The standard input shall be a text file.

INPUT FILES
       None.

ENVIRONMENT VARIABLES
       The following environment variables shall affect the execution of read:

       IFS    Determine the internal field separators used to delimit fields; see Shell Variables .

       LANG   Provide  a default value for the internationalization variables that are unset or null. (See the Base Definitions volume
              of IEEE Std 1003.1-2001, Section 8.2, Internationalization Variables for the precedence  of  internationalization  vari-
              ables used to determine the values of locale categories.)

       LC_ALL If set to a non-empty string value, override the values of all the other internationalization variables.

       LC_CTYPE
              Determine  the  locale for the interpretation of sequences of bytes of text data as characters (for example, single-byte
              as opposed to multi-byte characters in arguments).

       LC_MESSAGES
              Determine the locale that should be used to affect the format and contents of diagnostic messages  written  to  standard
              error.

       NLSPATH
              Determine the location of message catalogs for the processing of LC_MESSAGES .

       PS2    Provide the prompt string that an interactive shell shall write to standard error when a line ending with a backslash is
              read and the -r option was not specified, or if a here-document is not terminated after a <newline> is entered.

ASYNCHRONOUS EVENTS
       Default.

STDOUT
       Not used.

STDERR
       The standard error shall be used for diagnostic messages and prompts for continued input.

OUTPUT FILES
       None.

EXTENDED DESCRIPTION
       None.

EXIT STATUS
       The following exit values shall be returned:

        0     Successful completion.

       >0     End-of-file was detected or an error occurred.

CONSEQUENCES OF ERRORS
       Default.

       The following sections are informative.

APPLICATION USAGE
       The -r option  is  included  to  enable  read  to  subsume  the  purpose  of  the  line  utility,  which  is  not  included  in
       IEEE Std 1003.1-2001.

       The results are undefined if an end-of-file is detected following a backslash at the end of a line when -r is not specified.

EXAMPLES
       The following command:

              while read -r xx yy
              do
                  printf "%s %s\n" "$yy" "$xx"
              done < input_file

       prints a file with the first field of each line moved to the end of the line.

RATIONALE
       The  read  utility  historically  has been a shell built-in. It was separated off into its own utility to take advantage of the
       richer description of functionality introduced by this volume of IEEE Std 1003.1-2001.

       Since read affects the current shell execution environment, it is generally provided as a shell  regular  built-in.  If  it  is
       called in a subshell or separate utility execution environment, such as one of the following:

              (read foo)
              nohup read ...
              find . -exec read ... \;

       it does not affect the shell variables in the environment of the caller.

FUTURE DIRECTIONS
       None.

SEE ALSO
       Shell Command Language

COPYRIGHT
       Portions of this text are reprinted and reproduced in electronic form from IEEE Std 1003.1, 2003 Edition, Standard for Informa-
       tion Technology -- Portable Operating System Interface (POSIX), The Open Group  Base  Specifications  Issue  6,  Copyright  (C)
       2001-2003  by  the  Institute  of Electrical and Electronics Engineers, Inc and The Open Group. In the event of any discrepancy
       between this version and the original IEEE and The Open Group Standard, the original IEEE and The Open Group  Standard  is  the
       referee document. The original Standard can be obtained online at http://www.opengroup.org/unix/online.html .

IEEE/The Open Group                  2003                             READ(1P)


# read 实例

01.基本读取
read命令接收标准输入（键盘）的输入，或其他文件描述符的输入，得到输入后，read命令将数据放入一个标准变量中。
#!/bin/bash
echo -n "Enter your name:"                        //参数-n的作用是不换行，echo默认是换行
read  name                                        //从键盘输入
echo "hello $name,welcome to my program"          //显示信息

由于read命令提供了-p参数，允许在read命令行中直接指定一个提示。
所以上面的脚本可以简写成下面的脚本::
#!/bin/bash
read -p "Enter your name:" name
echo "hello $name, welcome to my program"
exit 0
在上面read后面的变量只有一个，也可以有多个，如果输入数据个数多过变量数，则最后所有的值都给最后一个变量。如果太少输入不会结束。

在read命令行中也可以不指定变量。如果不指定变量，那么read命令会将接收到的数据放置在环境变量REPLY中。
例如::
read -p "Enter a number"
环境变量REPLY中包含输入的所有数据，可以像使用其他变量一样在shell脚本中使用环境变量REPLY.
--------------------------------------------------------------------
02.计时输入
使用read命令存在着潜在危险。脚本很可能会停下来一直等待用户的输入。
如果无论是否输入数据脚本都必须继续执行，那么可以使用-t选项指定一个计时器。
-t选项  指定read命令等待输入的秒数。当计时满时，read命令返回一个非零退出状态。
#!/bin/bash
if read -t 5 -p "please enter your name:" name
then 
    echo "hello $name ,welcome to my script"
else
    echo "sorry,too slow"
fi
exit 0

除了输入时间计时，还可以通过-n选项设置read命令计数输入的字符。当输入的字符数目达到预定数目时，自动退出，并将输入的数据赋值给变量。
#!/bin/bash
read -n1 -p "Do you want to continue [Y/N]?" answer
case $answer in
Y | y)
      echo "fine ,continue";;
N | n)
      echo "ok,good bye";;
*)
     echo "error choice";;
esac
exit 0
该例子使用了-n选项，后接数值1，指示read命令只要接受到一个字符就退出。
只要按下一个字符进行回答，read命令立即接受输入并将其传给变量，无需按回车键。
--------------------------------------------------------------------
03.默读（输入不显示在监视器上）
有时会需要脚本用户输入，但不希望输入的数据显示在监视器上。典型的例子就是输入密码，当然还有很多其他需要隐藏的数据。
-s选项  能够使read命令中输入的数据不显示在监视器上（实际上，数据是显示的，只是 read命令将文本颜色设置成与背景相同的颜色）。
#!/bin/bash
read  -s  -p "Enter your password:" pass
echo "your password is $pass"
exit 0 
--------------------------------------------------------------------
04.读文件
可以使用read命令读取Linux系统上的文件。每次调用read命令都会读取文件中的"一行"文本，当文件没有可读的行时，read命令将以非零状态退出。
读取文件的关键是如何将文本中的数据传送给read命令。
最常用的方法是对文件使用cat命令并通过管道将结果直接传送给包含read命令的 while语句
例子:
#!/bin/bash
count=1                           //赋值语句，不加空格
cat test | while read line        //cat 命令的输出作为read命令的输入,read读到的值放在line中
do
   echo "Line $count:$line"
   count=$[ $count + 1 ]          //注意中括号中的空格。
done
echo "finish"
exit 0
------------------------------------------------------------------------------------------------------------
