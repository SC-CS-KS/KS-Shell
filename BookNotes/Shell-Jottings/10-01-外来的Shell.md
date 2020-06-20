# 外来的 shell

外来的 shell
对于大胆的开发人员，可基于您的需要或爱好使用替代的 shell。
Scheme shell (scsh) 提供了一种使用 Scheme（Lisp 语言的一种衍生物）的脚本环境。
Pyshell 是对创建使用 Python 语言的类似脚本的一次尝试。
最后，对于嵌入式系统，可以使用 BusyBox，它将一个 shell 和所有命令合并到一个二进制文件中，以简化其分发和管理。

清单 4 给出了以 Scheme shell (scsh) 编写的查找所有可执行文件的脚本。
这段脚本可能看起来很奇怪，但它实现了与我们目前所提供的脚本类似的功能。
这段脚本包含 3 个函数，直接使用可执行代码（在末尾）来测试参数数量。
这段脚本真正强大之处在showfiles 函数内，它迭代一个列表（在 with-cwd 后构造），在列表的每个元素后调用 write-ln。
这个列表通过迭代指定的目录并在其中过滤是可执行文件的文件来生成。

清单 4. 用 scsh 编写的查找所有可执行文件的脚本
#!/usr/bin/scsh -s
!#

(define argc
        (length command-line-arguments))

(define (write-ln x)
        (display x) (newline))

(define (showfiles dir)
        (for-each write-ln
                (with-cwd dir
                        (filter file-executable? (directory-files "." #t)))))

(if (not (= argc 1))
        (write-ln "Usage is fae.scsh dir")
        (showfiles (argv 1)))
----------------------------------------------------------------------------------------------------------------------------------------------
结束语
早期 shell 的许多理念和大量接口在之后的 35 年几乎未变 — 这是对早期 shell 的原始作者的贡献的有力证明。
在一个不断自我改造的行业中，shell 已大大改进，但没有发生重大变化。
尽管存在过创建特殊 shell 的尝试，但 Bourne shell 的衍生物仍然是所使用的主要 shell。
