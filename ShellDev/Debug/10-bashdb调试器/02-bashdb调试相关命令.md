# 02.bashdb 调试相关 命令

调试相关
H：  打印历史命令
H [from [to]]：打印从from到to的历史命令（from>=to），to缺省为0，from缺省为上一条历史命令（即打印全部）
H <-count>：打印倒数count条历史命令

q[uit] [ret] [n]：退出调试，ret为程序的返回值(缺省为0)，n表示退出嵌套调试（见“deb”命令）的层数（缺省为1）

i[nfo] [option]：查看由option指定的相关信息
    info a[rgs]：参数信息
    info b[reakpoints]：断点和监测点信息
    info d[isplay]：展示“display”命令设置的列表
    info files：源文件信息
    info functions：函数信息
    info l[ine]：当前行和文件名
    info p[rogram]：当前调试状态
    info si[gnals]：调试器对各种信号的处理方式
    info so[urce]：当前源文件信息
    info sources：同“info files”
    info stack：栈信息
    info terminal：终端信息
    info v[ariables]：所有变量信息，包括环境变量
    info w[atchpoints]：同“info b[reakpoints]”

!n 或 hi n：执行第n条历史命令（在bashdb提示符后的<>中有历史命令序号）
sh[ow]：显示设置信息
so[urce] <bashdb cmd file>：读入bashdb命令文件（每行一个bashdb命令）并批量执行
se[t]：做各种设置（“show”命令所输出的），如“set listsize 5”，则设置“l”命令默认打印行数为5
e[val] <shell cmd>：在当前环境中执行shell命令

disp[lay] <shell cmd>：
设置shell命令列表（每次执行增加一个），每次程序往下执行一步，都会依次执行该命令列表；
若不带shell命令参数，则打印已设置的shell命令列表。应该比较有用。

M：打印模块版本，一般用不到
x：
完整写法是“examine”，没太搞明白作用，运行“x var”或“x $var”（假如此前已定义var="aaa"），或“x 2+3”均有一定的输出。

deb[ug] <shell file>：
嵌套调试新的shell脚本，调试结束后返回当前调试，也可通过“quit”命令中途返回
!! <shell cmd> [args]：
手册说可以执行带参数的shell命令，但貌似“e”命令也一样可以。
file filename：
load filename：
