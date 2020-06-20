# shopt：开启和关闭shell特性

2. shopt：开启和关闭shell特性
内置命令shopt(shell option)开启、关闭和列出那些控制着shell行为的某些bash特性。

举例来说，下面的命令将使bash在扩展模糊文件引用时，包含那些以句号(.)开头的文件名，-s表示设置：
$ shopt -s dotglob
------------------------------------------------------------------------------------------
通过下面的命令可将这项特性关闭(默认)，-u表示取消设置：
$ shopt -u dotglob
------------------------------------------------------------------------------------------
如果只将特性的名称作为shopt唯一的参数，shell显示该特性是如何设置的：
$ shopt dotglob
dotglob          off
------------------------------------------------------------------------------------------
命令shopt不带任何选项或者参数时，将列出由shopt控制的那些特性及其状态。
命令shopt -s不带参数时，将列出那些由shopt控制并设置为开启的特性。
命令shopt –u将列出那些被取消或者关闭的bash特性。
表8-13列出了bash特性。
------------------------------------------------------------------------------------------
使用shopt设置set ±o特性  
提示 可以使用shopt设置或者取消那些由set ±o控制的特性。
使用常规的shopt –s或者-u语法，并包含-o选项。举例来说，下面的命令将开启noclobber特性：
$ shopt –o –s noclobber
------------------------------------------------------------------------------------------

## shop -s

shopt命令是set命令的一种替代，很多方面都和set命令一样，但它增加了很多选项。
“-p”选项来查看shopt选项的设置。
“-u”开 关表示一个复位的选项，
“-s”表示选项当前被设置。
 
# shopt -p    #查看当前设置情况
shopt -u cdable_vars
shopt -u cdspell
shopt -u checkhash
shopt -s checkwinsize
shopt -s cmdhist
shopt -u dotglob
shopt -u execfail
shopt -s expand_aliases
shopt -u extdebug
shopt -u extglob
shopt -s extquote
shopt -u failglob
shopt -s force_fignore
shopt -u gnu_errfmt
shopt -u histreedit
shopt -u histappend
shopt -u histverify
shopt -s hostcomplete
shopt -u huponexit
shopt -s interactive_comments
shopt -u lithist
shopt -s login_shell
shopt -u mailwarn
shopt -u no_empty_cmd_completion
shopt -u nocaseglob
shopt -u nullglob
shopt -s progcomp
shopt -s promptvars
shopt -u restricted_shell
shopt -u shift_verbose
shopt -s sourcepath
shopt -u xpg_echo

# shopt -s cdspell   #把cdspell选项打开
# shopt -p cdspell   #打印cdspell设置
shopt -s cdspell
# cd /raot           # 故意拼写错误
/root
# pwd                # 查看当前目录
/root
# cd /usr/loca/bin   #故意拼写错误
/usr/local/bin
# pwd              # 查看当前目录  
/usr/local/bin
# shopt -u cdspell #把cdspell选项打开
# shopt -p cdspell #打印cdspell设置
shopt -u cdspell
# cd /raot         #故意拼写错误
-bash: cd: /raot: 没有那个文件或目录     #进入目录错误

shopt命令选项
选项
含义 
cdable_vars
如果给cd内置命令的参数不是一个目录，就假设它是一个变量名，变量的值是将要转换到的目录
cdspell
纠正cd命令中目录名的较小拼写错误。检查的错误包括颠倒顺序的字符，遗漏的字符以及重复的字符。如果知道一处修改，正确的路径就打印出，命令将继续。只 用于交互式shell
checkhash
bash在试图执行一个命令前，先在哈希表中寻找，以确定命令是否存在。如果命令不存在，就执行正常路径搜索
checkwinsize
bash在每个命令后检查窗口大小，如果有必要，就更新LINES和COLUMNS的值
cmdhist
bash试图将一个多行命令的所有行保存在同一个历史项中。这使得多行命令的重新编辑更方便
dotglob
bash在文件名扩展的结果中包括以点（.）开头的文件名
execfail
如果一个交互式shell不能执行指定给exec内置命令作为参数的文件，它不会退出。如果exec失败，一个交互式shell不会退出 
expand_aliases 
别名被扩展。默认为打开 
extglob
打开扩展的模式匹配特征（正常的表达式元字符来自Korn shell的文件名扩展） 
histappend 
当shell退出时，历史清单将添加到以HISTFILE变量的值命名的文件中，而不是覆盖文件 
histreedit 
如果readline正被使用，用户有机会重新编辑一个失败的历史替换 
histverify
如果设置，且readline正被使用，历史替换的结果不会立即传递给shell解析器。而是将结果行装入readline编辑缓冲区中，允许进一步修 改 
hostcomplete 
如果设置，且readine正被使用，当正在完成一个包含@的词时bash将试图执行主机名补全。默认为打开 
huponexit 
如果设置，当一个交互式登陆shell退出时，bash将发送一个SIGHUP（挂起信号）给所有的作业 
interactive_comments 
在一个交互式shell中，允许以#开头的词以及同一行中其他的字符被忽略。默认为打开 
lithist 
如果打开，且cmdhist选项也打开，多行命令将用嵌入的换行符保存到历史中，而无需在可能的地方用分号来分隔 
mailwarn 
如果设置，且bash用来检查邮件的文件自从上次检查后已经被访问，将显示消息“The mail in mailfile has been read” 
nocaseglob 
如果设置，当执行文件名扩展时，bash在不区分大小写的方式下匹配文件名 
nullglob
如果设置，bash允许没有匹配任何文件的文件名模式扩展成一个空串，而不是它们本身 
promptvars 
如果设置，提示串在被扩展后再经历变量和参量扩展。默认为打开 
restricted_shell 
如果shell在受限模式下启动就设置这个选项。该值不能被改变。当执行启动文件时，不能复位该选项，允许启动文件发现shell是否是受限的 
sourcepath
如果设置，source内置命令使用PATH的值来寻找包含作为参数提供的文件的目录。默认为打开 
source 
点（.）的同义词 
shift_verbose
如果该选项设置，当移动计数超过位置参量个数时，shift内置命令将打印一个错误消息 

自Bash 3.1版开始，引入新选项 nocasematch，可让Bash在对比样式时忽略大小写。
shopt -s nocasematch  #打开选项
