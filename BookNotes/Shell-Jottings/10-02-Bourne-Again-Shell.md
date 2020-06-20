# Bourne-Again Shell

Bourne-Again Shell
Bourne-Again Shell（或 Bash）是一个开源 GNU 项目，旨在取代 Bourne shell。
Bash 由 Brian Fox 开发，已成为世上最流行的 shell 之一（出现在 Linux、Darwin、Windows®、Cygwin、Novell、Haiku 等系统中）。
顾名思义，Bash 是 Bourne shell 的一个超集，大部分 Bourne 脚本都可原封不动地执行。

除了支持脚本的向后兼容性，Bash 还整合了来自 Korn 和 C shell 的功能。
您将找到命令历史、命令行编辑、一个目录栈（pushd 和popd）、许多有用的环境变量和命令完成等。

Bash 继续在发展，拥有许多新功能，支持正则表达式（类似于 Perl）和关联数组。
尽管其中一些功能可能在其他脚本语言中不存在，但可以编写兼容其他语言的脚本。
对于这一点，清单 3 中所示的示例脚本等同于 Korn shell 脚本（来自 清单 2），除了 shebang 区别 (/bin/bash)。

清单 3. 用 Bash 编写的查找所有可执行文件的脚本
#!/bin/bash
# find all executables

count=0

# Test arguments
if [ $# -ne 1 ] ; then
  echo "Usage is $0 <dir>"
  exit 1
fi

# Ensure argument is a directory
if [ ! -d  "$1" ] ; then
  echo "$1 is not a directory."
  exit 1
fi

# Iterate the directory, emit executable files
for filename in "$1"/*
do
  if [ -x "$filename" ] ; then
    echo $filename
    count=$((count+1))
  fi
done

echo
echo "$count executable files found."

exit 0

这些 shell 之间的一个关键区别是它们的发布所依据的许可。
您可能已猜到：
Bash 是在 GNU 项目中开发的，是依据 GPL 发布的。
csh、tcsh、zsh、ash 和 scsh 都是依据 BSD 或一种类似 BSD 的许可来发布的。
Korn shell 可依据 Common Public License 使用。
----------------------------------------------------------------------------------------------------------------------------------------------
