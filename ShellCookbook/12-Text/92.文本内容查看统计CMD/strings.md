# strings

NAME
       strings - print the strings of printable characters in files.

SYNOPSIS
       strings [-afovV] [-min-len]
               [-n min-len] [--bytes=min-len]
               [-t radix] [--radix=radix]
               [-e encoding] [--encoding=encoding]
               [-] [--all] [--print-file-name]
               [-T bfdname] [--target=bfdname]
               [--help] [--version] file...


## strings 用法

strings - 显示文件中的可打印字符
strings 一般用来查看非文本文件的内容.
              
[-a|-|--all] [-f|--print-file-name] [-o] [--help] [-v|--version] 
[-n min-len|-min-len|--bytes=min-len] [-t {o,x,d}[--target=bfdname] |--radix={o,x,d}] file

描述 (DESCRIPTION)
For each file given, GNU strings 显示每个指定的 file 文件里，
包含的所有 有4个(或者用选项指定的数字) 以上连续可打印字符的字符串, 在之后紧跟着一个不可打印的字符。
默认情况 下, 它只显示目标文件初始化 和 装载段的字符串; 对于其它类型的文件它显示整个文件里包含的所有字符串。   
strings 一般用来查看非文本文件的内容。

选项 (OPTIONS)
在 这里 并列 的 长选项 和 短选项 都是 等价的.
-a  --all  不只是扫描目标 文件 初始化 和 装载 段, 而是 扫描 整个 文件.
-f --print-file-name 在 显示 字符串 之前 先 显示 文件名.
--help 在 标准 输出 打印 strings 命令 的 选项 列表，然后 退出.
-v  --version 在 标准 输出 打印 strings 命令 的 版本号 然后 退出.
-n min-len -min-len
-bytes=min-len  打印 至少 min-len 字符 长 的 字符串. 默认的 是 4.
-t {o,x,d}
--radix={o,x,d}
在 字符串 前面 显示 其在 文件 中 的 偏移量. 选项 中 的 单个 字符 指定 偏移量的 进制 是 八进制, 十六进制 还 是 十进制.
--target=bfdname 指定 一种 目标 代码 格式 来 代替 你的 系统的 默认 格式. 关于 可用的 目标 代码格式 请 参见 objdump(1).
-o     同 -t o.

可见strings能输出文件中的可打印字符串（可指定字符串的最小长度），
通常用来查看非文本文件（如二进制可执行文件）中的可读内容。

比如：

```bash
$strings /lib/tls/libc.so.6 | grep GLIBC
GLIBC_2.0
GLIBC_2.1
GLIBC_2.1.1
GLIBC_2.1.2
GLIBC_2.1.3
GLIBC_2.2
GLIBC_2.2.1
GLIBC_2.2.2
GLIBC_2.2.3
GLIBC_2.2.4
GLIBC_2.2.6
GLIBC_2.3
GLIBC_2.3.2
GLIBC_2.3.3
GLIBC_2.3.4
GLIBC_PRIVATE
```

这样就能看到glibc支持的版本。
