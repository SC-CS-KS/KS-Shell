Table of Contents
=================

   * [变量条件替换](#变量条件替换)
      * [${var:-string} 和 ${var-string}](#var-string-和-var-string)
      * [${var: string} 和 ${var string}](#varstring-和-varstring)
      * [${var:=string} 和 ${var=string}](#varstring-和-varstring-1)
      * [${var:?message} 和 ${var?message}](#varmessage-和-varmessage)
      * [${var#pattern} 和 ${var##pattern}](#varpattern-和-varpattern)
      * [${var\xpattern} 和 ${var\x\xpattern}](#varpattern-和-varpattern-1)
      * [${value/pattern/string} 和 ${value//pattern/string}](#valuepatternstring-和-valuepatternstring)
      * [${string/#substring/replacement}](#stringsubstringreplacement)
      * [${string/\xsubstring/replacement}](#stringsubstringreplacement-1)
      * [${#value}](#value)
      * [${var:offset}/${var:offset:length}](#varoffsetvaroffsetlength)
      * [${!varprefix*} 与 ${!varprefix@}](#varprefix-与-varprefix)
   * [BASH_BUILTINS 内建命令](#bash_builtins-内建命令)
      * [alias / unalias [别名]](#alias--unalias-别名)
      * [builtin](#builtin)
      * [command](#command)
      * [enable](#enable)
      * [fc](#fc)
      * [set](#set)
      * [source](#source)
      * [trap](#trap)
      * [umask](#umask)
      * [wait](#wait)
   * [BASH 中的内建变量](#bash-中的内建变量)
      * [PS 终端提示符](#ps-终端提示符)
         * [PS1 一级提示符](#ps1-一级提示符)
         * [PS2 二级提示符，多行交互提示符](#ps2-二级提示符多行交互提示符)
         * [PS3 select命令使用的提示符](#ps3-select命令使用的提示符)
         * [PS4 调试输出追踪](#ps4-调试输出追踪)
      * [BASH_SOURCE](#bash_source)
      * [BASH_REMATCH](#bash_rematch)
   * [Expansion](#expansion)
      * [Brace expansion](#brace-expansion)
      * [Tilde expansion](#tilde-expansion)
      * [Parameter expansion](#parameter-expansion)
      * [Command substitution](#command-substitution)
      * [Arithmetic Expansion](#arithmetic-expansion)
      * [Process Substitution](#process-substitution)
      * [Word splitting](#word-splitting)
      * [Filename Expansion](#filename-expansion)
      * [Quote Removal](#quote-removal)

# 变量条件替换
## ${var:-string} 和 ${var-string}
```md
${var:-string} 当变量已定义，且为非空值，返回变量值，否则返回string值。 
${var-string} 当变量已定义，返回变量值，否则返回string值。 
注意：不改变的var值 
```
```bash
#unset var
#echo ${var:-sunny}
sunny
#var=
#echo ${var:-sunny}
sunny
#var=chan
#echo ${var:-sunny}
chan
# -----------------------------------------
#unset var         
#echo ${var-sunny}
sunny
#var=
#echo ${var-sunny}
返回Null
#var=chan          
#echo ${var-sunny}
chan
```
## ${var:+string} 和 ${var+string}
```md
${var:+string} 当变量已定义，且为非空值，返回string值，否则返回Null。
${var+string} 当变量已定义，返回string值，否则返回Null。
注意：不改变var的值
```
```bash 
$ unset var         
$ echo ${var:+sunny}

$ var=
$ echo ${var:+sunny}

$ var=chan
$ echo ${var:+sunny}
sunny
# --------------------------------
$ unset var         
$ echo ${var+sunny} 

$ var=
$ echo ${var+sunny}
sunny

var=chan
echo ${var+sunny}
```
## ${var:=string} 和 ${var=string}
```md
${var:=string} 当变量已定义，且为非空值，返回变量值，否则设置变量值为string，并返回其值。 
${var=string} 当变量已定义，返回变量值，否则设置变量值为string，并返回其值。 
     
注意：同时改变var的值
```
```md
# unset A
# echo ${A=my}  
my
# unset A     
# echo ${A:=my}
my
# A=           
# echo ${A=my} 

# A=           
# echo ${A:=my}
my
# A=va
# echo ${A=my}
va
# A=va
# echo ${A:=my}
va 

比较：
-------------------------------------------------------------- 
# unset A      
# echo ${A:-my}
my
# echo $A

# unset A      
# echo ${A:+my} 

# echo $A      

# unset A      
# echo ${A:=my} 
my
# echo $A 
```
## ${var:?message} 和 ${var?message}
```md
${var:?message} 
当变量已定义，且为非空值，返回var的值。否则显示 var:message，并退出当前命令，或脚本。
如果省略message，会打印默认信息parameter null or not set。

注意：在交互模式下，shell无须退出。

如果变量未定义，则退出。

${var?message}
该替换，在变量已定义条件下执行，效果与${var:?message} 一致。
```
```md
# unset A
# echo ${A?my}  
-bash: A: my
# unset A     
# echo ${A:?my}
-bash: A: my
# A=
# echo ${A?my} 

# A=
# echo ${A:?my}
-bash: A: my
# A=va 
# echo ${A?my} 
va
# A=va
# echo ${A:?my}
va

#unset A
#echo ${A:?message}  
bash: A: message
#echo ${A:?}       
bash: A: parameter null or not set
```
## ${var#pattern} 和 ${var##pattern}
```md
${var#pattern} 
如果模式匹配于变量开头处，则删除匹配最短的部分，返回剩余部分。

${var##pattern}     
去掉var中与pattern相匹配的部分，条件是var的开头与pattern相匹配。
#与##的区别在于一个是最短匹配模式，一个是最长匹配模式。 
--------------------------------------------------------------
注意：
变量的条件替换中的模式匹配，都是“通配符(wildcard)”模式。
--------------------------------------------------------------
#pwd=`pwd`      
#echo $pwd      
/home/dcache/bin
#echo ${pwd#/*/}
dcache/bin
#echo ${pwd##/*/}
bin
#echo $pwd       
/home/dcache/bin

注意：pattern是通配符，上面的 // 不是正则表达式，/是普通字符
```
## ${var%pattern} 和 ${var%%pattern}
```md
${var%pattern} 
${var%%pattern} 
------------------------
与#即#用法相似，只是是从var的尾部与pattern相匹配，%与%%的区别与#与##一样。 
---------------------------------------------------------------------------
#echo $pwd      
/home/dcache/bin/
#echo ${pwd%/*/}
/home/dcache
#echo ${pwd%%/*/}
返回空
```
## ${value/pattern/string} 和 ${value//pattern/string}
```md
${value/pattern/string} 
${value//pattern/string} 
进行变量内容的替换，把与pattern匹配的部分替换为string的内容，/与//的区别与上同。 
```
## ${string/#substring/replacement}
```md
如果$string的前缀匹配$substring, 那么就用$replacement来代替匹配到的$substring
说明：”* $substring”可以是一个正则表达式
```
## ${string/%substring/replacement}
```md
如果$string的后缀匹配$substring, 那么就用$replacement来代替匹配到的$substring
说明：”* $substring”可以是一个正则表达式
```
## ${#value}
```md
${#value} 
POSIX标准化长度字符串运算符，返回变量的字符个数 。
--------------------------------------------------------------
#echo $pwd       
/home/dcache/bin/
#echo ${#pwd}
17

#word=Supercalifragilisticexpialidocious
#echo There are ${#word} characters in $word
```
## ${var:offset}/${var:offset:length}
```md
按照offset和length的设定对var进行截取，并返回该值。
${var:offset} 
${var:offset:length} 
不是设置 length 的时候，是从偏移位截取到字符末尾。
注意变量var值不变。
-------------------------------------------------------------------
1.顺序截取
#A=chenguang
# echo ${A:4}
guang
# A=chenguang
# echo ${A:4}
guang
# echo ${A:0:4}
chen
# echo ${A:5:9}   
uang

#echo ${A}
chenguang
-----------------------------------------------------
2.反向截取
截取最后一位
#echo ${A:${#A}-1:1}  
g

截取倒数第三和第二位
$ echo ${A:${#A}-3:2}
an
```
## ${!varprefix*} 与 ${!varprefix@}
```md
${!varprefix*}与${!varprefix@} 相似，可以通过变量名前缀字符，搜索已经定义的变量,无论是否为空值。

$ var1=11; var2=12; var3=
$ echo ${!v@}            
var1 var2 var3
$ echo ${!v*}
var1 var2 var3
```

# BASH_BUILTINS 内建命令
```md
  bash,  :, ., [, alias, bg, bind, break, builtin, cd, command, compgen, complete, continue, declare, dirs, disown, echo, enable, eval, exec,
       exit, export, fc, fg, getopts, hash, help, history, jobs, kill, let, local, logout, popd, printf, pushd, pwd, read, readonly, return,  set,
       shift, shopt, source, suspend, test, times, trap, type, typeset, ulimit, umask, unalias, unset, wait - bash built-in commands, see bash(1)
```
```md
调用外部命令处理，与内置操作符性能相差非常大。在Shell编程中，尽量用内置操作符或者函数完成。
```
## alias / unalias [别名]
```md
当命令被读取时，Shell会展开别名定义，别名主要用于交互式Shell。
如：alias 'rm=rm -i' 指删除时，需要确认。
Shell不会做递归的别名展开，所有此定义是合法的。
```
## builtin
```md
Run a shell builtin.  
builtin [shell-builtin [arg ...]]   
This is useful when you wish to rename a shell builtin to be a function, 
but need the functionality of the builtin within the function itself.
```
## command
```md
NAME
       command - execute a simple command
SYNOPSIS
       command [-p] command_name [argument ...]
       command [ -v | -V ] command_name
PROLOG
       This manual page is part of the POSIX Programmer’s Manual.  The Linux implementation of this interface may differ
       (consult the corresponding Linux manual page for details of Linux behavior), or the interface may not  be  imple-
       mented on Linux.
```
## enable
```md
Enable and disable builtin shell commands.  
This allows you to use a disk command(磁盘操作命令) which has the same name as a shell builtin without specifying a full pathname. 
enable [-pnds] [-a] [-f filename] [name ...]
Options
	If no non-option names are given, or the -p option is supplied, a list of builtins is printed.  
	-n
		The -n option displays a list of all disabled builtins.
```
## fc
```md
fc is used to list or edit and re-execute commands from the history list.
fc [-e ename] [-nlr] [first] [last] or fc -s [pat=rep] [cmd]
```
## set
```md
set [--abefhkmnptuvxBCHP] [-o option] [arg ...]
Options
	        -a  Mark variables which are modified or created for export.
	        -b  Notify of job termination immediately.
	        -e  Exit immediately if a command exits with a non-zero status.
	        -f  Disable file name generation (globbing).
	        -h  Remember the location of commands as they are looked up.
	        -k  All assignment arguments are placed in the environment for a command, not just those that precede the command name.
	        -m  Job control is enabled.
	        -n  Read commands but do not execute them.
		-o  option-name
		Set the variable corresponding to option-name:
			                allexport    same as -a
			                braceexpand  same as -B
			                emacs        use an emacs-style line editing interface
			                errexit      same as -e
			                errtrace     same as -E
			                functrace    same as -T
			                hashall      same as -h
			                histexpand   same as -H
			                history      enable command history
			                ignoreeof    the shell will not exit upon reading EOF
			                interactive-comments
				 allow comments to appear in interactive commands
			                keyword      same as -k
			                monitor      same as -m
			                noclobber    same as -C
			                noexec       same as -n
			                noglob       same as -f
			                nolog        currently accepted but ignored
			                notify       same as -b
			                nounset      same as -u
			                onecmd       same as -t
			                physical     same as -P
			                pipefail     the return value of a pipeline is the status of
                             the last command to exit with a non-zero status,
                             or zero if no command exited with a non-zero status
			                posix        change the behavior of bash where the default
                             operation differs from the 1003.2 standard to
                             match the standard
			                privileged   same as -p
			                verbose      same as -v
			                vi           use a vi-style line editing interface
			                xtrace       same as -x
	        -p  Turned on whenever the real and effective user ids do not match.
            Disables processing of the $ENV file and importing of shell
            functions.  Turning this option off causes the effective uid and
            gid to be set to the real uid and gid.
	        -t  Exit after reading and executing one command.
	        -u  Treat unset variables as an error when substituting.
	        -v  Print shell input lines as they are read.
	        -x  Print commands and their arguments as they are executed.
	        -B  the shell will perform brace expansion
	        -C  If set, disallow(驳回，不允许) existing regular files to be overwritten(覆盖) by redirection(重定向) of output.
	        -E  If set, the ERR trap is inherited by shell functions.
	        -H  Enable ! style history substitution.  This flag is on by default when the shell is interactive.
	        -P  If set, do not follow symbolic links when executing commands such as cd which change the current directory.
	        -T  If set, the DEBUG trap is inherited by shell functions.
	        -   Assign any remaining arguments to the positional parameters.
		            The -x and -v options are turned off.
```
## source
```md
Read and execute commands from FILENAME and return.  
	注：该命令通常用命令“.”来替代。如：source .bash_rc 与 . .bash_rc 是等效的。
	source命令(从 C Shell 而来)是bash shell的内置命令。点命令，(从Bourne Shell而来)是source的另一名称。
source filename [arguments]
```
## trap
```md
The command ARG is to be read and executed when the shell receives signal(s) SIGNAL_SPEC.  
	trap [-lp] [arg signal_spec ...]
```
## umask
```md
The user file-creation mask is set to MODE. 
	umask [-p] [-S] [mode]
Options
	If MODE is omitted, or if `-S' is supplied, the current value of the mask is printed. 
	The `-S' option makes the output symbolic; otherwise an octal number is output.
	If `-p' is supplied, and MODE is omitted, the output is in a form that may be used as input.  
	If MODE begins with a digit, it is interpreted as an octal number, otherwise it is a symbolic mode string like that accepted by chmod(1).
```
## wait
```md
等待进程执行完后返回
	该指令常用于shell脚本编程中，待指定的指令执行完成后，才会继续执行后面的任务。
	该指令等待作业时，在作业标识号前必须添加备份号"%"。
		$ wait %1               #等待作业号为1的作业完成 
wait [n]
	If N is not given, all currently active child processes are waited for,and the return code is zero.  
	N is a process ID; if it is not given, all child processes of the shell are waited for.
```
# BASH 中的内建变量
```md
PPID : 执行当前bash的用户的进程ID
PWD : 目前的工作目录(Print Working Directory。
OLDPWD : 上一个工作目录,可以使用 cd -在最近两个目录间切换
REPLY : 当read命令没有参数时，直接设在REPLY上。
UID : 登录系统的用户ID
EUID : 当前用户的ID
BASH : Bash的完整路径。
BASH_VERSION : Bash版本。
SHLVL : bash的层数，每执行一次bash命令，就自动加1
RANDOM : 产生一个随机数，并且不需要种子
SECONDS : 当前shell从开始到现在运行的秒数
OPTARG : getopts处理的最後一个选项参数
OPTIND : 下一个要由getopts所处理的参数号码
HOSTTYPE: 机器种类
MACHTYPE:机器类型
OSTYPE : 作业系统名称。
IFS : 默认分隔符
PATH : 命令自动寻找的路径,类似于windows的环境变量
HOME : 当前用户的“家”目录，可以用 ~ 代替
CDPATH : cd命令的搜寻路径
LINENO : 显示刚执行的命令是第几个命令，登录后执行的命令从1开始，每执行一个就自动加1
HISTCMD : 当前记录的历史命令的个数,等同于 history|wc -l
ENV : 如果这个参数被设定，每次有shell script被执行时，将会执行它所设定的档名做为环境设定
MAIL : 如果这个参数被设定，而且MAILPATH没有被设定，那麽有信件进来时，bash会通知使用者。
MAILCHECK : 设定多久时间检查邮件一次
MAILPATH : 一串的邮件检查路径
MAIL_WARNING : 如果有设定的话，邮件被读取後，将会显示讯息
HISTSIZE : 记录的历史命令的条数
HISTFILE : 记录历史命令的文件，默认是~/.bash_history
HISTFILESIZE : 历史命令文件的最大值
OPTERR: 如果设为1，bash会显示getopts的错误
PROMPT_COMMAND : 该变量的值是一个或一组命令，如果设置了的话，每次执行命令前都先执行这里的命令
IGNOREEOF : 将EOF值当成输入，内定为10。
TMOUT: 超时时间，如果用户在超过该变量设置的时间内没有执行任何命令，就自动logout
```
## PS 终端提示符
### PS1 一级提示符
```md
是设置命令提示符(普通用户为 $ ,root 为#)之前的内容的变量。
默认情况下PS1为”\s-\v\$”，显示为: -bash-3.2$
```
```md
转义字符代表的信息:
\h--主机名
\u--用户名
\w--当前工作目录的绝对路径 如：/usr/bin
\W--当前工作目录的基址名 如上例中的bin
\t--当前的系统时间：HH:MM:SS（24小时制）
\@--当前的系统时间，12小时制， 如：07:14 PM
\d--如“Wed May 28”格式的日期
\n--换行符
\$?--上以命令执行后的返回值，成功执行返回0，否则返回一个非零的数
\!--该条命令在历史命令中的编号
\#--该条命令在这个shell中的编号
```
### PS2 二级提示符，多行交互提示符
```md
设置换行或执行do 等命令后的提示符，默认为>
如：
当输入一个超过一行的命令时，可以使用“\”符号将命令分割成几行来执行，当使用了‘\’之后在第二行会出现一个新的提示符。
PS2变量也支持PS1中的属性（\h,\t等）以及各种颜色设置。
```
### PS3 select命令使用的提示符

### PS4 调试输出追踪
```md
使用set -x 或 bash -x 执行脚本时，追踪脚本详细运行过程的提示符，默认为+

PS4="$0 $LINENO+: "
$0:脚本的名字
$LINENO: 显示当前命令在脚本中的行数
```
## BASH_SOURCE
```md
BASH_SOURCE 最常见的用法：获取当前执行脚本路径。
如果是sh test.sh或者./test.sh的运行方式，那么很简单，直接使用$0就可以了。 

那么如果是source test.sh或者. ./test.sh的方式运行呢？
$0就变成了”-bash”了。
这个时候就需要使用$BASH_SOURCE了。（bash版本>=3.0）

用法：
${BASH_SOURCE[0]-$0}
```
## BASH_REMATCH
```md
<List>
    <Job id="1" name="abc"/>
    <Job id="2" name="zyz"/>
    <Job id="3" name="beew"/>
</List>

想要得到这个结果：
abc | 1
zyz | 2
beew | 3
```
```bash
while read  line; do
  if [[ $line =~ id=\"([0-9]+).*name=\"([^\"]*) ]]; then
    echo "${BASH_REMATCH[2]} | ${BASH_REMATCH[1]}"
  fi
done < file
```
* 双目运算符=~
```md
和==以及!=具有同样的优先级。
如果使用了它，则其右边的字符串就被认为是一个扩展的正则表达式来匹配。

如果字符串和模式匹配，则返回值是0，否则返回1。
如果这个正则表达式有语法错误，则整个条件表达式的返回值是2。
```
```md
如果打开了Shell的 nocasematch 选项，则匹配时不考虑字母的大小写。
模式的任何部分都可以被引用以强制把其当作字符串来匹配。
由正则表达式中括号里面的子模式匹配的字符串被保存在数组变量 BASH_REMATCH 中。
```
```md
BASH REMATCH 中下标为0的元素是字符串中与整个正则表达式匹配的部分。
BASH REMATCH 中下标为n的元素是字符串中与第 n 个括号里面的子模式匹配的部分。
```
# Expansion
```md
命令行的扩展是在拆分成词之后进行的。
有七种类型的扩展：  
brace expansion(花括号扩展),   
tilde expansion(波浪线扩展), 
parameter and variable expansion(参数和变量扩展), 
command substitution(命令替换), 
arithmetic expansion(算术扩展), 
word splitting(词的拆分), 和 pathname expansion(路径扩展).  
 
扩展的顺序是：
brace  expansion,  tilde expansion, parameter, variable 
和 arithmetic expansion 还有 command substitution  (按照从左到右的顺序), 
word  splitting,  最后是 pathname expansion.  
还有一种附加的扩展：
process subtitution (进程替换) 只有在支持它的系统中有效(POSIX不支持)。  
       
只有    brace   expansion,   word   splitting,   
和   pathname   expansion  在扩展前后的词数会发生改变；
其他扩展总是将一个词扩展为一个词。唯一的例外是  "$@"  和  "${name[@]}"  (参见PARAMETERS参数)。  
```
## Brace expansion
```sh
$echo {10..0}
10 9 8 7 6 5 4 3 2 1 0
```
```md
Brace    expansion    是一种可能产生任意字符串的机制。这种机制类似于    pathname    expansion,  但是并不需要存在相应的文件。              
花括号扩展的模式是一个可选的preamble(前导字符),  后面跟着一系列逗号分隔的字符串，包含在一对花括号中，再后面是一个可选的postscript(附言)。  
前导被添加到花括号中的每个字符串前面，附言被附加到每个结果字符串之后， 从左到右进行扩展。  
花括号扩展可以嵌套。
扩展字符串的结果没有排序，而是保留了从左到右的顺序。 例如， a{d,c,b}e 扩展为 `ade ace abe'。  
花括号扩展是在任何其他扩展之前进行的，任何对其他扩展有特殊意义的字符都保留在结果中。它是严格字面上的。        
Bash不会对扩展的上下文或花括号中的文本做任何语义上的解释。  
正确的花括号扩展必须包含没有引用的左括号和右括号，以及至少一个没有引用的逗号。任何不正确的表达式都不会被改变。
可以用反斜杠来引用 { 或 ,  来阻止将它们识别为花括号表达式的一部分。 为了避免与参数扩展冲突，字符串 ${ 不被认为有效的组合。  

这种结构通常用来简写字符串的公共前缀远比上例中为长的情况，例如：  
              mkdir /usr/local/src/bash/{old,new,dist,bugs}  
       或者：  
              chown root /usr/{ucb/{ex,edit},lib/{ex?.?*,how_ex}}  
花括号扩展导致了与历史版本的 sh 的一点不兼容。在左括号或右括号作为词的一部分出现时， sh  不会对它们进行特殊处理，会在输出中保留它们。  
Bash   将括号从花括号扩展结果的词中删除。
例如，向   sh  输入  file{1,2}  会导致不变的输出。
同样的输入在  bash 进行扩展之后，会输出 file1 file2   
如果需要同 sh 严格地保持兼容，需要在启动 bash 的时候使用 +B  选项，或者使用  set 命令加上 +B 选项来禁用花括号扩展 
(参见下面的 shell 内建命令(SHELL BUILTIN COMMANDS) 章节)。 
---------------------------------------------------------------------------------------------------------------------------
示例1：操作多个文件名有共同点的文件
$ ls
bk
$ touch file{source,target,info,readme}.txt
$ ls
bk  fileinfo.txt  filereadme.txt  filesource.txt  filetarget.txt

$ echo file{1,2,3,4}.txt
file1.txt file2.txt file3.txt file4.txt
$ touch file{1,2,3,4}.txt
$ ls
bk  file1.txt  file2.txt  file3.txt  file4.txt

示例2：一个命令将几个文件名有规律的文件转移到一个目录
$ls bk
$ mv file{1,2,3,4}.txt bk/.
$ ls bk
file1.txt  file2.txt  file3.txt  file4.txt

示例3：备份
$touch mydata.log
$ vi back.sh
$ chmod +x back.sh 
$ cat back.sh 
set -x #调试模式
bkdate=`date +%F` #日期
cp mydata{,$bkdate}.log #将log备份为带日期格式

$ls
back.sh  bk  mydata.log
$ ./back.sh 
+++ date +%F
++ bkdate=2010-06-10
++ cp mydata.log mydata2010-06-10.log
$ ls
back.sh  bk  mydata2010-06-10.log  mydata.log

示例4 处理多个有规律文件名
$ echo /var/log/messages.{1..3}
/var/log/messages.1 /var/log/messages.2 /var/log/messages.3

$ echo file{a..d}{1..3}.txt
filea1.txt filea2.txt filea3.txt fileb1.txt fileb2.txt fileb3.txt filec1.txt filec2.txt filec3.txt filed1.txt filed2.txt filed3.txt

示例5 for循环
$ for i in {1..9..3}
> do
> echo $i
> done
1
4
7
```
## Tilde expansion
```md
   Tilde Expansion
       If  a  word  begins with an unquoted tilde character ('~'), all of the characters preceding the first unquoted slash (or all characters, if
       there is no unquoted slash) are considered a tilde-prefix.  If none of the characters in the tilde-prefix are quoted, the characters in the
       tilde-prefix  following  the tilde are treated as a possible login name.  If this login name is the null string, the tilde is replaced with
       the value of the shell parameter HOME.  If HOME is unset, the home directory of the user executing the shell is substituted instead.   Oth-
       erwise, the tilde-prefix is replaced with the home directory associated with the specified login name.

       If  the tilde-prefix is a '~+', the value of the shell variable PWD replaces the tilde-prefix.  If the tilde-prefix is a '~-', the value of
       the shell variable OLDPWD, if it is set, is substituted.  If the characters following the tilde in the tilde-prefix consist of a number  N,
       optionally prefixed by a '+' or a '-', the tilde-prefix is replaced with the corresponding element from the directory stack, as it would be
       displayed by the dirs builtin invoked with the tilde-prefix as an argument.  If the characters following the tilde in the tilde-prefix con-
       sist of a number without a leading '+' or '-', '+' is assumed.

       If the login name is invalid, or the tilde expansion fails, the word is unchanged.

       Each  variable  assignment  is checked for unquoted tilde-prefixes immediately following a : or =.  In these cases, tilde expansion is also
       performed.  Consequently, one may use file names with tildes in assignments to PATH, MAILPATH,  and  CDPATH,  and  the  shell  assigns  the
       expanded value.
```
## Parameter expansion

## Command substitution
```md
$(command) or 'command'
```
## Arithmetic Expansion
```md
Arithmetic expansion allows the evaluation of an arithmetic expression and the substitution of  the  result.   The  format  for  arithmetic
       expansion is:

              $((expression))

       The  expression  is  treated  as  if it were within double quotes, but a double quote inside the parentheses is not treated specially.  All
       tokens in the expression undergo parameter expansion, string expansion, command substitution, and quote removal.  Arithmetic expansions may
       be nested.

       The  evaluation is performed according to the rules listed below under ARITHMETIC EVALUATION.  If expression is invalid, bash prints a mes-
       sage indicating failure and no substitution occurs.
```
## Process Substitution
```md
<(list)
or
>(list)
```
```md
在支持命名管道(FIFOs)或者'/dev/fd'的系统上可以实现Process Substitution,基本格式如下
    command <(list)
    or  
    command >(list)
开始list的运行的时候会把list的输入输出连接到一个FIFO或者某个`/dev/fd'下的文件。
而整个扩展的结果就是把这个文件的文件名作为参数传递给命令command。

如果使用>(list)的话，command写入这个文件的内容，就会作为list的输入。
而如果使用<(list)的话， 那么list的输出就可以使command从这个文件中读到。
注意<，>与左括号之间是没有空格的，否则的话会被解释成为重定向操作。
在允许的情况下 process substitution和parameter expansion， variable expasion, command expansion 和arithmatic expasion一起被执行.

好处，能解决一些重定向不能解决的问题，如让一个命令有多个输入的情况：
有一个例子，要求将2个文件，a和b的内容进行查找，找到唯一的行，然后输出。
下面是采用redirection的方法：
# sort a | uniq >tmp1
# sort b | uniq >tmp2
# comm -3 tmp1 tmp2
a
f
g
# rm tmp1 tmp2
如果使用process substitution，可以这样做：
# comm -3 <(sort a | uniq) <(sort b | uniq)
a
f
g
 这样明显效率提高了。
---------------------------------------------------------------------------------------------------------------------------
cmd|→tee--→stdout→|cmd2
            ↓            ↑
             →cmd1→
             
echo 123 | tee >(awk '{ print "Length of " $0 " is " length($0) }') | tr 3 c
12c
Length of 12c is c

echo 123 | tee >(awk '{ print "Length of " $0 " is " length($0) }' >tmpfile) | tr 3 c ;cat tmpfile
12c
Length of 123 is 3

echo "Hello from: "|tee > >(sed 's/$/sed 1/') >(sed 's/$/sed 2/') >(awk '{printf $0"awk\n"}')
---------------------------------------------------------------------------------------------------------------------------
Q：
现在有一个进程替换的问题向大家请教：
当直接在bash的交互式命令中输入：
while read name; do echo $name; done< <(echo "
dfasdf
dsfa
afda"|grep -Ev "(dsfa)|(^ *$)")
这样执行的话是没有问题的，但当把它放入到一个脚本文件中进行执行的时候：
#!/bin/bash
while read name; do echo $name; done< <(echo "
dfasdf
dsfa
afda"|grep -Ev "(dsfa)|(^ *$)") 

却出现了如下的错误：
test.sh: line 3: syntax error near unexpected token `<'
test.sh: line 3: `while read name; do echo $name; done< <(echo "'

A：
在一般的linux系统当中，使用sh调用执行脚本相当于打开了bash的POSIX标准模式也就是说 /bin/sh 相当于 /bin/bash --posix
所以，sh跟bash的区别，实际上就是bash有没有开启posix模式的区别,而进程替换非POSIX兼容的，所以会出现错误。
因为main (int argc, char ** argv ) , argv[0] 可以取得对应可执行文件的名字, 在sh->bash情况下, argv[0] == 'sh'，所以就可以区别对待了!

而如果直接使用./test.sh 执行，脚本使用第一行指定的解释器来解释执行。
```
## Word splitting

## Filename Expansion

## Quote Removal
