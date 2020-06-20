# exec



## 02.exec /source

01. exec 执行程序
 虽然exec和source都是在父进程中直接执行，但exec这个与source有很大的区别：
 source    是执行shell脚本，而且执行后会返回以前的shell。
 exec       是在当前shell下执行指定的程序，直接把当前登陆shell作为一个程序看待，在其上经行替换程序内容。
               执行不会返回以前的shell了，除非调用指定程序失败（如程序不存在）。

举例说明：
$ exec ls
exp1  exp5  linux-2.6.27.54  ngis_post.sh  test  xen-3.0.1-install
<logout>

$exec duddd ;echo execute failed   
-bash: exec: duddd: not found
execute failed
--------------------------------------------------------------------------------------------------------------------------------

## 03.exec 重定向

02. exec的重定向
$ cd /dev/fd
$ ls
0  1  2  255
$ ll
lrwx------ 1 dcache users 64 Jul 25 11:07 0 -> /dev/pts/0
lrwx------ 1 dcache users 64 Jul 25 11:07 1 -> /dev/pts/0
lrwx------ 1 dcache users 64 Jul 25 11:01 2 -> /dev/pts/0
lrwx------ 1 dcache users 64 Jul 25 11:07 255 -> /dev/pts/0

默认会有这四个项：
0 是标准输入，默认是键盘。
1 是标准输出，默认是屏幕。
2 是标准错误，默认也是屏幕。

当执行exec 3>test时：
#   exec 3>/root/test/test 
#   ls
0  1  2  255  3
多了个3，也就是又增加了一个设备，这时fd3就相当于一个管道，重定向到fd3中的文件会被写在test中。
关闭这个重定向可以用exec 3>&-。

# who >&3
# ls >&3
# exec 3>&-
# cat /root/test/test 
root     tty1         2010-11-16 01:13
root     pts/0        2010-11-15 22:01 (192.168.0.1)
root     pts/2        2010-11-16 01:02 (192.168.0.1)
0
1
2
255
3
--------------------------------------------------------------------------------------------------------------------------------

## 04.exec 实例

03. 实例：
实例一：
exec 3<test              #将test文件定位到文件描述符3，读取fd3就是读取test文件
while read -u 3 pkg       #-u通过文件描述符来指定读取文件     
do
  echo "$pkg"
done
exec 3<&-

实例二：
# exec 4<&1  
文件描述符fd4指向屏幕设备文件（当前标准输出），此时文件描述符fd1和fd4都指向屏幕设备。
# ls >&4
ls的结果会输出到屏幕
#exec >file1
把标准输出变更为 file1
#ls
此时ls标准输出到file1文件，fd1指向的文件。
#exec 1<&4
将fd4复制给fd1，fd4指向屏幕，则fd1也指向屏幕，即恢复屏幕为标准输出。
--------------------------------------------------------------------------------------------------------------------------------


## man exec

NAME
       exec - execute commands and open, close, or copy file descriptors
SYNOPSIS
       exec [command [argument ...]]
DESCRIPTION
       The exec utility shall open, close, and/or copy file descriptors as specified by any redirections as part of the command.

       If exec is specified without command or arguments, and any file descriptors with numbers greater than 2 are  opened  with
       associated  redirection  statements,  it is unspecified whether those file descriptors remain open when the shell invokes
       another utility. Scripts concerned that child shells could misuse(误用、滥用) open file descriptors can always close them explicitly(明确地),
       as shown in one of the following examples.

       If  exec is specified with command, it shall replace the shell with command without creating a new process.  If arguments
       are specified, they shall be arguments to command.  Redirection affects the current shell execution environment.

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
       If command is specified, exec shall not return to the shell; rather, the exit status of the process  shall  be  the  exit
       status  of  the program implementing command, which overlaid(overlay覆盖) the shell. If command is not found, the exit status shall be
       127. If command is found, but it is not an executable utility, the exit status shall  be  126.  If  a  redirection  error
       occurs  (see  Consequences of Shell Errors ), the shell shall exit with a value in the range 1-125. Otherwise, exec shall
       return a zero exit status.
CONSEQUENCES OF ERRORS
       Default.
       The following sections are informative.
APPLICATION USAGE
       None.
EXAMPLES
       Open readfile as file descriptor 3 for reading:
              exec 3< readfile

       Open writefile as file descriptor 4 for writing:
              exec 4> writefile

       Make file descriptor 5 a copy of file descriptor 0:
              exec 5<&0

       Close file descriptor 3:
              exec 3<&-

       Cat the file maggie by replacing the current shell with the cat utility:
              exec cat maggie

RATIONALE
       Most historical implementations were not conformant(一致) in that:
              foo=bar exec cmd
       did not pass foo to cmd.
