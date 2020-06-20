# 50.Shell Trap

## 01.Shell Traps 1

01.rm –f a.txt && echo “OK”
当a.txt不存在时，依然输出OK
原因：
-f, --force
ignore nonexistent files, never prompt
-f 通常被理解为强制删除，和man中的解释还是有一定出入的，实际上应理解为忽略不存在的文件，从来不提示。
解决：
如果要区分文件是否存，先判断文件是否存在再删除，去掉-f，判断rm 的返回值。


## 50.fork 炸弹

在Linux系统下执行这段代码 :(){ :|:& }；： 就会引起死机，一旦执行起来后，唯一的方法就是重启系统。
实际上这段代码是一段无限递归代码，将系统资源耗尽。

本文下面有这段代码的详细解释，为了防止fork炸弹，方法就是限制用户能够启动的进程数。
具体做法，编辑/etc/security/limits.conf文件，在末尾加入 ：
* hard nproc 200 
将用户的进程数限制为200，经过测试，root账户不受这个限制。

Q. Can you explain following bash code or bash fork() bomb?
:(){ :|:& };:

A. This is a bash function. 
It gets called recursively (recursive function). This is most horrible code for any Unix / Linux box. 
It is often used by sys admin to test user processes limitations
 (Linux process limits can be configured via /etc/security/limits.conf and PAM).

Once a successful fork bomb has been activated in a system it may not be possible to resume normal operation without rebooting,
as the only solution to a fork bomb is to destroy all instances of it.
WARNING! These examples may crash your computer if executed.

Understanding :(){ :|:& };: fork() bomb code
:() - It is a function name. It accepts no arguments at all. Generally, bash function is defined as follows:
foo(){
arg1=$1
echo ''
#do_something on $arg argument
}

fork() bomb is defined as follows:
:(){
:|:&
};:

:|: - Next it call itself using programming technique called recursion and pipes the output to another call of the function ':'. 
The worst part is function get called two times to bomb your system.

& - Puts the function call in the background so child cannot die at all and start eating system resources.

; - Terminate the function definition

: - Call (run) the function aka set the fork() bomb.

Here is more human readable code:
bomb() {
bomb | bomb &
}; bomb

Properly configured Linux / UNIX box should not go down when fork() bomb sets off.
----------------------------------------------------------------------------------------------------------------------------------------
作为 Linux/Unix 系统上内核与用户之间的接口，shell 由于使用方便、可交互能力强、具有强大的编程能力等特性而受到广泛的应用。
bash（Bourne Again shell）是对 Bourne shell 的扩展，并且综合了很多 csh 和 Korn Shell 中的优点，使得 bash 具有非常灵活且强大的编程接口。
bash 所提供的诸如命令补齐、通配符、命令历史记录、别名之类的新特性，使其迅速成为很多用户的首选。

然而，作为一种 解释性语言，bash 在编程能力方面提供的支持并不像其他编译性的语言（例如 C 语言）那样完善，执行效率也会低很多。
这些缺点在编写函数（尤其是递归函数）时都展现的一览无余。
本文将从经典的 fork 炸弹入手，逐一介绍在 bash 中编写递归函数时需要注意问题，并探讨各种问题的解决方案。
尽管本文是以 bash 为例介绍相关概念，但是类似的思想基本上也适用于其他 shell。

函数在程序设计中是一个非常重要的概念，它可以将程序划分成一个个功能相对独立的代码块，使代码的模块化更好，结构更加清晰。
并可以有效地减少程序的代码 量。递归函数更是充分提现了这些优点，通过在函数定义中调用自身，可以将复杂的计算问题变成一个简单的迭代算法。
当回溯到边界条件时，再逐层返回上一层函 数。有很多数学问题都非常适合于采用递归的思想来设计程序求解，例如阶乘、汉诺（hanoi）塔等。

可能很多人都曾经听说过 fork 炸弹，它实际上只是一个非常简单的递归程序。
程序所做的事情只有一样：不断 fork 一个新进程。
由于程序是递归的，如果没有任何限制，这会导致这个简单的程序迅速耗尽系统里面的所有资源。

在 bash 中设计这样一个 fork 炸弹非常简单，Jaromil 在 2002 年设计了最为精简的一个 fork炸弹的实现。
整个程序从函数定义到调用仅仅包含 13 个字符，如清单 1 所示。

清单1. bash 中的 fork 炸弹
.(){ .|.& };.
这串字符乍看上去根本就看不出个所以然来，下面让我们逐一解释一下它究竟在干些什么。
为了解释方便，我们对清单1中的内容重新设置一下格式，并在前面加上了行号，如清单 2 所示。

清单2. bash 中的 fork 炸弹的解释 
  1 .()
  2 {
  3  .|.& 
  4 }
  5 ;
  6 .

第 1 行说明下面要定义一个函数，函数名为小数点，没有可选参数。
第 2 行表示函数体开始。
第 3 行是函数体真正要做的事情，首先它递归调用本函数，然后利用管道调用一个新进程（它要做的事情也是递归调用本函数），并将其放到后台执行。
第 4 行表示函数体结束。
第 5 行并不会执行什么操作，在命令行中用来分隔两个命令用。从总体来看，它表明这段程序包含两个部分，首先定义了一个函数，然后调用这个函数。
第 6 行表示调用本函数。

对于函数名，大家可能会有所疑惑，小数点也能做函数名使用吗？
毕竟小数点是 shell 的一个内嵌命令，用来在当前 shell 环境中读取指定文件，并运行其中的命令。
实际上的确可以，这取决于 bash 对命令的解释顺序。

默认情况下，bash 处于非 POSIX 模式，此时对命令的解释顺序如下：
关键字，例如 if、for 等。
别名。别名不能与关键字相同，但是可以为关键字定义别名，例如 end=fi。
特殊内嵌命令，例如 break、continue 等。
POSIX 定义的特殊内嵌命令包括：
.（小数点）、:（冒号）、break、continue、eval、exec、exit、export、readonly、 return、set、shift、times、trap 和 unset。
bash 又增加了一个特殊的内嵌命令 source。

函数。如果处于非 POSIX 模式，bash 会优先匹配函数，然后再匹配内嵌命令。
非特殊内嵌命令，例如 cd、test 等。
脚本和可执行程序。在 PATH 环境变量指定的目录中进行搜索，返回第一个匹配项。

由 于默认情况下，bash 处于非 POSIX 模式，因此 fork 炸弹中的小数点会优先当成一个函数进行匹配。
（实际上，Jaromil 最初的设计并没有使用小数点，而是使用的冒号，也能起到完全相同的效果。）

要使用 POSIX 模式来运行 bash 脚本，可以使用以下三种方法：
使用 --posix 选项启动 bash。
在运行 bash 之后，执行 set -o posix 命令。
使用 /bin/sh 。

最后一种方法比较有趣，尽管 sh 在大部分系统上是一个指向 bash 的符号链接，但是它所启用的却是 POSIX 模式，所有的行为都完全遵守 POSIX 规范。
在清单 3 给出的例子中，我们可以发现，小数点在默认 bash 中被解释成一个函数，能够正常执行；
但是在 sh 中，小数点却被当作一个内嵌命令，因此调用函数时会被认为存在语法错误，无法正常执行。

清单3. bash 与 sh 对命令匹配顺序的区别
# ls -l /bin/bash /bin/sh
-rwxr-xr-x 1 root root 735144 2007-08-31 22:20 /bin/bash
lrwxrwxrwx 1 root root      4 2007-12-18 13:26 /bin/sh -> bash

# echo $SHELL
/bin/bash

# .() { echo hello; } ; .
hello

# sh
sh-3.2# echo $SHELL
/bin/bash

sh-3.2# .() { echo hello; } ; .
sh: `.': not a valid identifier
sh: .: filename argument required
.: usage: . filename [arguments]
sh-3.2#

一旦运行清单 1 给出的 fork 炸弹，会以2的指数次幂的速度不断产生新进程，这会导致系统资源会被迅速耗光。
最终除非重新启动机器，否则基本上就毫无办法了。
为了防止这会造成太大的损 害，我们可以使用 ulimit 限制每个用户能够创建的进程数，如清单 4 所示。

清单4. 限制用户可以创建的进程数
# ulimit -u 128
# ulimit -a
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
max nice                        (-e) 20
file size               (blocks, -f) unlimited
pending signals                 (-i) unlimited
max locked memory       (kbytes, -l) unlimited
max memory size         (kbytes, -m) unlimited
open files                      (-n) 1024
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) unlimited
max rt priority                 (-r) unlimited
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) 128
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited

# .() { .|.& } ; .
[1] 6152

# bash: fork: Resource temporarily unavailable
bash: fork: Resource temporarily unavailable
bash: fork: Resource temporarily unavailable
...

在清单 4 中，我们将用户可以创建的最大进程数限制为 128，执行 fork 炸弹会迅速 fork 出大量进程，此后会由于资源不足而无法继续执行。


