# 01.在Shell脚本中输出调试信息

二. 在shell脚本中输出调试信息
通过在程序中加入调试语句把一些关键地方或出错的地方的相关信息显示出来是最常见的调试手段。
Shell程序员通常使用echo(ksh程序员常使用print)语句输出信息。

但仅仅依赖echo语句的输出跟踪信息很麻烦，调试阶段在脚本中加入的大量的echo语句在产品交付时还得再费力一一删除。
针对这个问题，本节主要介绍一些如何方便有效的输出调试信息的方法。

## 01.使用 trap 命令

1. 使用trap命令
trap命令用于捕获指定的信号并执行预定义的命令。
其基本的语法是:
trap 'command' signal
其中signal是要捕获的信号，command是捕获到指定的信号之后，所要执行的命令。
可以用kill –l命令看到系统中全部可用的信号名，捕获信号后所执行的命令可以是任何一条或多条合法的shell语句，也可以是一个函数名。

shell脚本在执行时，会产生三个所谓的“伪信号”，
(之所以称之为“伪信号”是因为这三个信号是由shell产生的，而其它的信号是由操作系统产生的)，
通过使用trap命令捕获这三个“伪信号”并输出相关信息对调试非常有帮助。

表 1. shell伪信号
信号名	    何时产生
EXIT	    从一个函数中退出或整个脚本执行完毕
ERR	    当一条命令返回非零状态时(代表命令执行不成功)
DEBUG	    脚本中每一条命令执行之前

通过捕获EXIT信号，我们可以在shell脚本中止执行或从函数中退出时，输出某些想要跟踪的变量的值。
并由此来判断脚本的执行状态以及出错原因,其使用方法是：
trap 'command' EXIT　或　trap 'command' 0

通过捕获ERR信号,可以方便的追踪执行不成功的命令或函数，并输出相关的调试信息。
以下是一个捕获ERR信号的示例程序，其中的$LINENO是一个shell的内置变量，代表shell脚本的当前行号。
$ cat -n exp1.sh
     ERRTRAP()
     {
         echo "[LINE:$1] Error: Command or function exited with status $?"
      }
      foo()
      {
         return 1;
      }
      trap 'ERRTRAP $LINENO' ERR
      abc
      foo  

其输出结果如下：
$ sh exp1.sh
exp1.sh: line 10: abc: command not found
[LINE:10] Error: Command or function exited with status 127
[LINE:11] Error: Command or function exited with status 1
      
在调试过程中，为了跟踪某些变量的值，我们常常需要在shell脚本的许多地方插入相同的echo语句来打印相关变量的值，这种做法显得烦琐而笨拙。
而通过捕获DEBUG信号，我们只需要一条trap语句就可以完成对相关变量的全程跟踪。
以下是一个通过捕获DEBUG信号来跟踪变量的示例程序:
$ cat –n exp2.sh
    #!/bin/bash
    trap 'echo “before execute line:$LINENO, a=$a,b=$b,c=$c”' DEBUG
    a=1
    if [ "$a" -eq 1 ]
    then
       b=2
    else
       b=1
    fi
    c=3
    echo "end"

其输出结果如下：
$ sh exp2.sh
before execute line:3, a=,b=,c=
before execute line:4, a=1,b=,c=
before execute line:6, a=1,b=,c=
before execute line:10, a=1,b=2,c=
before execute line:11, a=1,b=2,c=3
end

从运行结果中可以清晰的看到每执行一条命令之后，相关变量的值的变化。
同时，从运行结果中打印出来的行号来分析，可以看到整个脚本的执行轨迹，能够判断出哪些条件分支执行了，哪些条件分支没有执行。

## 02.使用 tee 命令

2. 使用tee命令
在shell脚本中管道以及输入输出重定向使用得非常多，在管道的作用下，一些命令的执行结果直接成为了下一条命令的输入。
如果发现由管道连接起来的一批命令的执行结果并非如预期的那样，就需要逐步检查各条命令的执行结果来判断问题出在哪儿。
但因为使用了管道，这些中间结果并不会显示在屏幕上，给调试带来了困难，此时就可以借助于tee命令了。

tee命令会从标准输入读取数据，将其内容输出到标准输出设备,同时又可将内容保存成文件。
例如有如下的脚本片段，其作用是获取本机的ip地址：
ipaddr=`/sbin/ifconfig | grep 'inet addr:' | grep -v '127.0.0.1'
| cut -d : -f3 | awk '{print $1}'` 

echo $ipaddr

运行这个脚本，实际输出的却不是本机的ip地址，而是广播地址,这时我们可以借助tee命令，输出某些中间结果，将上述脚本片段修改为：
ipaddr=`/sbin/ifconfig | grep 'inet addr:' | grep -v '127.0.0.1'
| tee temp.txt | cut -d : -f3 | awk '{print $1}'`
echo $ipaddr

之后，将这段脚本再执行一遍，然后查看temp.txt文件的内容：
$ cat temp.txt
inet addr:192.168.0.1  Bcast:192.168.0.255  Mask:255.255.255.0

可以发现中间结果的第二列(列之间以:号分隔)才包含了IP地址，而在上面的脚本中使用cut命令截取了第三列。
故只需将脚本中的cut -d : -f3改为cut -d : -f2即可得到正确的结果。

具体到上述的script例子，也许并不需要tee命令的帮助，比如可以分段执行由管道连接起来的各条命令并查看各命令的输出结果来诊断错误。
但在一些复杂的shell脚本中，这些由管道连接起来的命令可能又依赖于脚本中定义的一些其它变量。
这时想要在提示符下来分段运行各条命令就会非常麻烦了，简单地在管道之间插入一条tee命令来查看中间结果会更方便一些。

## 03.使用"调试钩子"

3. 使用"调试钩子"
在C语言程序中，我们经常使用DEBUG宏来控制是否要输出调试信息，在shell脚本中我们同样可以使用这样的机制，如下列代码所示：
if [ “$DEBUG” = “true” ]; then
echo “debugging”                                #此处可以输出调试信息
fi

这样的代码块通常称之为“调试钩子”或“调试块”。
在调试钩子内部可以输出任何您想输出的调试信息，使用调试钩子的好处是它是可以通过DEBUG变量来控制的。
在脚本的开发调试阶段，可以先执行export DEBUG=true命令打开调试钩子，使其输出调试信息。
而在把脚本交付使用时，也无需再费事把脚本中的调试语句一一删除。

如果在每一处需要输出调试信息的地方均使用if语句来判断DEBUG变量的值，还是显得比较繁琐。
通过定义一个DEBUG函数可以使植入调试钩子的过程更简洁方便，如下面代码所示:
$ cat –n exp3.sh
     DEBUG()
     {
     if [ "$DEBUG" = "true" ]; then
         $@　　
     fi
     }
     a=1
     DEBUG echo "a=$a"
     if [ "$a" -eq 1 ]
     then
         b=2
    else
         b=1
    fi
    DEBUG echo "b=$b"
    c=3
    DEBUG echo "c=$c"

在上面所示的DEBUG函数中，会执行任何传给它的命令，并且这个执行过程是可以通过DEBUG变量的值来控制的。
我们可以把所有跟调试有关的命令都作为DEBUG函数的参数来调用，非常的方便。

