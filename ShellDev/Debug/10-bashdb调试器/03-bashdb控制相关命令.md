# 03.bashdb 控制相关 命令

控制相关
T [n]：  查看n层栈信息，缺省为全部（等价命令：where或backtrace）
s[tep] [n]：  前进（遇到函数会进入）n步，缺省为1
n[ext] [n]：  前进（遇到函数不进入）n步，缺省为1
回车：  重复上一个“n”或“s”命令
c[ontinue] [line no]：  继续执行到指定行，缺省为下一断点或结束，若指定行为空行或注释行，则与缺省相同
L：打印所有断点和监测点，类似“info b”
b[reak] <line no>：设置断点
de[lete] <breakpoint no>：删除断点
D：删除所有断点
sk[ip] [n]：跳过接下来的n条语句不执行，缺省为1
cl[ear] [line no]：清除指定行上的所有断点，缺省为当前行
d [line no]：同上
R [shell file] [args]：重新启动新的shell脚本调试，当前调试强制结束，缺省为重启当前脚本调试
u[p] [n]：回溯n层调用栈，缺省为1
do[wn] [n]：展望n层调用栈，缺省为1
W [var]：设置变量var的监测点，若不带参数会提示是否删除所有监测点（等价命令watch）
We [expr]：
t[oggle]：
en[able]/di[sable] n：使断点n有效或失效，手册说对监测点和展现点也有效，但没试成功
tb[reak] [line no]：设置只中断一次的断点，一般应该是在循环中有用
a <line no> <shell cmd>：在指定行执行前，执行shell命令，即action
A：删除所有action
ret[urn]：中断当前函数（或source file）的执行，直接返回
finish：将当前函数（或source file）执行完成，返回主调函数
cond[ition] <breakpoint no> <expression>：设置条件断点，expression怎么写没搞明白
p[rint]：打印变量值
V [[!]pattern]：打印符合pattern的所有变量，加！表示打印补集，缺省表示pattern取*，pattern貌似只支持*正则。比如定义了“NAME”变量，pattern应为“NAME”或“*AM*”，用“AM”不行
S [[!]pattern]：打印符合pattern的所有函数，加！表示打印补集，缺省表示pattern取*

