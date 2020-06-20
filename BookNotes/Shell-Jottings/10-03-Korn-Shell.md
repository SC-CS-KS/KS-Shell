# Korn shell

Korn shell
Korn shell (ksh) 由 David Korn 设计，是在与 Tenex C shell 相同的时期引入的。
Korn shell 的一项最有趣的功能是，它除了向后兼容原始的 Bourne shell，还可用作脚本语言。

Korn shell 在 2000 年以开源形式发布（依据 Common Public License）以前一直是专用的软件。
除了提供与 Bourne shell 强大的向后兼容性，Korn shell 还包含其他 shell 的功能（比如 csh 的历史功能）。
该 shell 还提供了可在现代脚本语言（比如 Ruby 和 Python）中找到的一些更加高级的功能 — 例如，关联数组和浮点算法。
Korn shell 可用于多种操作系统，包括 IBM® AIX® 和 HP-UX，致力于支持 Portable Operating System Interface for UNIX (POSIX) shell 语言标准。
Korn shell 是 Bourne shell 的衍生物，因此看上去更像 Bourne shell 和 Bash 而不是 C shell。

让我们看一个查找可执行文件的 Korn shell 示例（清单 2）。
清单 2. 用 ksh 编写的查找所有可执行文件的脚本
#!/usr/bin/ksh
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

在清单 2 中，您将注意到的第一点是它与 清单 1 的相似性。
在结构上，该脚本基本上是相同的，但在执行条件、表达式和迭代的方式上存在明显的区别。
没有采用类似 C 的测试操作符，ksh 采用了典型的 Bourne 风格操作符（-eq、-ne 和 -lt 等）。

Korn shell 也有用一些与迭代相关的区别。
在 Korn shell 中，使用了 for in 结构，使用命令替换来表示从命令 ls "$1"/*（表示指定子目录的内容）的标准输出创建的文件列表。

除了上面定义的其他功能，Korn 还支持别名功能（用于将一个词替换为用户定义的字符串）。
Korn 还有其他许多功能默认已禁用（比如文件名称完成），但可由用户启用。
----------------------------------------------------------------------------------------------------------------------------------------------
