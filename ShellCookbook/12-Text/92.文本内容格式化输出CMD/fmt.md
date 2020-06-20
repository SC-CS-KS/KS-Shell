# fmt

格式：
fmt    [-cstu]    [-p<列起始字符串>]    [-w<每列字符数>]    [--help]    [--version]    [文件名]


功能：
按照指定格式编排文本文件


参数：
        -c或--crown-margin                         ：             每段前两列缩排
        -p<列起始字符串>或-prefix=<列起始字符串>   ：             仅合并含有指定字符串的列，通常运用在程序语言的注解方面
        -s或--split-only                           ：             只拆开字数超出每列字符数的列，但不合并字数不足每列字符数的列
        -t或--tagged-paragraph                     ：             每列前两列缩排，但第1列和第2列的缩排格式不同
        -u或--uniform-spacing                      ：             每个字符之间都以一个空格字符间隔，每个句子之间则两个空格字符分隔
        -w<每列字符数>或--width=<每列字符数>或-<每列字符数>：     设置每列的最大字符数

        --help：显示在线帮助
        --version：显示版本信息


NAME
       fmt - simple optimal text formatter

SYNOPSIS
       fmt [-DIGITS] [OPTION]... [FILE]...

DESCRIPTION
       Reformat each paragraph in the FILE(s), writing to standard output.  If no FILE or if FILE is `-', read standard input.
