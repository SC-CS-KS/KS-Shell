# 41.Shell 特性

表8-13  bash特性
特    性
描    述
语    法
备 用 语法

allexport
在执行这条命令之后自动输出用户创建的所有变量和函数。
set -o allexport
set -a

braceexpand
使bash执行花括号扩展(默认)
set -o
braceexpand
set -B

cdspell
纠正传递给cd的参数中目录名中小的拼写错误
shopt -s cdspell
 
cmdhist
保存一个多行命令中的所有文本行，如果需要将添加分号
shopt -s cmdhist
 
dotglob
使模糊文件引用中的shell特殊字符(通配符)来匹配以句号(.)开头的文件名；默认情况下，这些特殊字符并不匹配开头的句号；必须总要显式地指定文件名.和..，因为没有模式曾匹配它们
shopt -s dotglob
 
emacs
为命令行编辑指定emacs模式(默认)
set -o emacs
 
errexit
使bash在遇到简单的命令失败(而不是控制结构)时退出。
set -o errexit
set -e
execfail
使shell脚本在找不到exec参数所指的文件时继续运行。默认情况下，当exec找不到其参数指定的文件时，脚本将终止
shopt -s execfail
 




expand_aliases
使别名扩展(默认情况下，对于交互式shell是开启的，而对于非交互式shell是关闭的)
shopt -s
expand_alias
 
hashall
使bash记住它使用PATH找到的命令的位置
set -o hashall
set -h
histappend
在shell退出时，使bash把历史列表追加到HISTFILE命令的文件后面。
默认情况下，bash会覆盖掉这个文件。
shopt -s
histappend
 
histexpend
使历史机制(使用感叹号)工作(默认)。
关闭这个特性将关闭历史扩展。
set -o histexpand
set -H
history
开启命令历史特性(默认情况下是开启的)
set -o history
 
ignoreeof
指定bash接收到10次EOF字符之后才能退出；在信号不好的链路上很有用
set -o ignoreeof
 
monitor
启用作业控制(默认启用)
set -o monitor
set -m
nocaseglob
使模糊文件引用匹配文件名而不区分大小写(默认关闭)
shopt -s
nocastglob
 
noclobber
阻止文件覆盖(默认关闭)
set -o noclobber
set -C
noglob
关闭路径名扩展(默认关闭)
set -o noglob
set -f
notify
如果启用了作业控制，将立即报告后台作业的终结状态。
默认行为是在显示下一个提示符之前显示该状态。
set -o notify
set -b
nounset
当在交互式shell中使用未设置变量时，将显示一条错误消息并退出shell脚本
set -o unset
set -u
nullglob
使bask扩展模糊文件引用，这些模糊文件引用不能将文件名与一个空字符相匹配。默认情况下，bash无需扩展这些文件引用就可以传递它们
shopt-s nullglob
 
posix
以POSIX模式返回bash
set-o posix
 
verbose
当bask读取命令行时显示这些命令行
set-o verbose
set-v
vi
为命令行的编辑指定vi编辑模式
set-o vi
 
xpg_echo
使内置命令cd在无需-e选项的情况下扩展反斜线转义序列
shopt –s xpg_echo
 
xtrace
启用shell调试功能
set-o xtrace
set-x

nolog  关闭函数定义的命令历史记录功能。
noexec 读取命令，并且检查语法错误，但不要执行。交互式Shell被允许忽略此项。                                      -n

以上蓝色字体部分为POSIX标准Shell选项，如果可移植性问题比较重要，不要使用POSIX标准化以外的选项。
-----------------------------------------------------------------------------------------------------------------------------------------------------


