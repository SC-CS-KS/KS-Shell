# Tenex C shell

Tenex C shell
C shell 是 Bill Joy 1978 年在加州大学伯克利分校攻读研究生期间为 Berkeley SoftwareDistribution (BSD) UNIX 系统开发的。
5 年后，该 shell 引入了来自 Tenex 系统（在 DEC PDP 系统上很流行）的功能。Tenex 引入了文件名和命令完成功能，以及命令行编辑功能。
Tenex C shell (tcsh) 仍然向后兼容 csh，但改进了它的整体交互式功能。tcsh 是 Ken Greer 在 Carnegie Mellon University 开发的。

该 C shell 的一个重要的设计目标是创建一种类似 C 语言的脚本语言。
这是一个有用的目标，因为 C 语言是所使用的主要语言（此外，该操作系统也是主要使用 C 语言开发的）。

Bill Joy 在 C shell 中引用的一项有用功能是命令历史。此功能维护以前执行的命令的历史，允许用户检查并轻松选择之前的命令来执行。
例如：键入命令 history 将显示以前执行的命令。可使用向上和向下箭头来选择命令，或者可以使用 !! 执行前一个命令。
也可以引用以前的命令的参数，例如 !* 引用前一个命令的所有参数，其中 !$ 引用前一个命令的最后一个参数。

看一下 tcsh 脚本的一个简单示例（清单 1）。
这段脚本获取一个参数（一个目录名称），输出该目录中的所有可执行文件以及找到的文件数量。我将在每个示例中重用此脚本设计来演示区别。

tcsh 脚本可分解为 3 个基本部分。
首先，请注意，我使用了 shebang 或 hashbang 符号来将此文件声明为可由定义的 shell 可执行文件（在本例中为 tcsh 二进制文件）解释。
这允许我以常规可执行文件的形式执行该文件，而不在它之前添加解释器二进制文件。它维护找到的可执行文件数量，所以我将此数量初始化为 0。

清单 1. 用 tcsh 编写的查找所有可执行文件的脚本
#!/bin/tcsh
# find all executables

set count=0

# Test arguments
if ($#argv != 1) then
  echo "Usage is $0 <dir>"
  exit 1
endif

# Ensure argument is a directory
if (! -d  $1) then
  echo "$1 is not a directory."
  exit 1
endif

# Iterate the directory, emit executable files
foreach filename ($1/*)
  if (-x $filename) then
    echo $filename
    @ count = $count + 1
  endif
end

echo
echo "$count executable files found."

exit 0

第一部分测试用户传递的参数。#argv 变量表示传入的参数数量（不包括命令名称本身）。
您可指定这些参数的索引来访问它们：
例如，#1 表示第一个参数（它是 argv[1] 的简写）。该脚本需要一个参数；
如果它未找到该参数，则输出一条错误消息，使用 $0 表示在控制台输入的命令名称（argv[0]）。

第二部分确保传入的参数是一个目录。
如果该参数是一个目录，-d 操作符返回 True。
但请注意，我首先指定了一个 ! 符号，这表示无效。这样，表达式可表明，如果参数不是一个目录，则输出一条错误消息。

最后一部分迭代目录中的文件，以测试它们是否可执行文件。
我使用方便的 foreach 迭代器，它循环括号（在本例中为该目录）中的每一项，然后在循环中测试每一项。
这一步使用 -x 操作符测试文件是否为可执行文件，如果是，则输出该文件并将数量加一。在脚本的末尾，我输出可执行文件的数量。
