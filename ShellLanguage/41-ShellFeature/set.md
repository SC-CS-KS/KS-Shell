# set -o

set命令可以用来定制shell环境，使用选项“o”来打开或者关闭选项。例如打开选项：set -o 选项，关闭选项目：set +o 选项。
例如要打开vi交互式命令行编辑，则如下：    
$ set -o                             #查看当前设置情况
allexport       off
braceexpand     on
emacs           on
errexit         off
errtrace        off
functrace       off
hashall         on
histexpand      on
history         on
ignoreeof       off
interactive-comments    on
keyword         off
monitor         on
noclobber       off
noexec          off
noglob          off
nolog           off
notify          off
nounset         off
onecmd          off
physical        off
pipefail        off
posix           off
privileged      off
verbose         off
vi              off             #关闭中
xtrace          off

# set -o vi   #把vi选项打开
# set -o      #查看当前设置情况
allexport       off
braceexpand     on
emacs           off
errexit         off
errtrace        off
functrace       off
hashall         on
histexpand      on
history         on
ignoreeof       off
interactive-comments    on
keyword         off
monitor         on
noclobber       off
noexec          off
noglob          off
nolog           off
notify          off
nounset         off
onecmd          off
physical        off
pipefail        off
posix           off
privileged      off
verbose         off
vi              on                #已经打开
xtrace          off


内置set命令选项
选项名 
快捷开关
含义 
allexport 
-a 
从这个选项中被设置开始就自动标明要输出的新变量或修改过的变量，直至选项被复位 
braceexpand 
-B
打开花括号扩展，它是一个默认设置 
emacs 
 
使用emacs内置编辑器进行命令行编辑，是一个默认设置 
errexit
-e 
当命令返回一个非零退出状态（失败）时退出。读取初始化文件时不设置 
histexpand 
-H 
执行历史替换时打开!和!!扩展，是一个默认设置 
history 
 
打开命令行历史、默认为打开 
ignoreeof
 
禁止用EOF(Ctrl+D)键退出shell。必须键入exit才能退出。等价于设置shell变量IGNOREEOF=10 
keyword 
-k  
将关键字参数放到命令的环境中 
interactive-comments
 
对于交互式shell，把#符后面的文本作为注释 
monitor
-m 
设置作业控制 
noclobber
-C 
防止文件在重定向时被重写 
noexec
-n 
读命令，但不执行。用来检查脚本的语法。交互式运行时不开启 
noglob 
-d 
禁止用路径名扩展。即关闭通配符 
notify
-b 
后台作业完成时通知用户 
nounset
-u  
扩展一个未设置的变量时显示一个错误信息 
onecmd
-t 
在读取和执行命令后退出 
physical
-P  
设置时，在键入cd或pwd禁止符号链接。用物理目录代替 
privileged
-p 
设置后，shell不读取.profile或ENV文件，且不从环境继承shell函数，将自动为setuid脚本开启特权 
verbose 
-v 
为调试打开verbose模式 
vi
 
使用vi内置编辑器进行命令行编辑 
xtrace
-x 
为调试打开echo模式 
 
 