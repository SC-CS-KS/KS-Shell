# col

col 用法

功能说明：过滤控制字符。 

语　　法：col [-bfx][-l<缓冲区列数>] 

补充说明：
在许多UNIX说明文件里，都有RLF控制字符。
当我们运用shell特殊字符">"和">>"，把说明文件的内容输出成纯文本文件时，控制字符会变成乱码，col指令则能有效滤除这些控制字符。 

参　　数： 
    -b   过滤掉所有的控制字符，包括RLF和HRLF。 
    -f   滤除RLF字符，但允许将HRLF字符呈现出来。 
    -x   以多个空格字符来表示跳格字符。 
    -l<缓冲区列数>   预设的内存缓冲区有128列，您可以自行指定缓冲区的大小。

RLF、HRLF控制字符：
逆向换行符       Reverse/Retrorse Line Feed： ESC-7(escape then 7)    
半逆向换行符    Half Reverse Line Feed：ESC-8 (escape then 8)

## man col

COL(1)                                              BSD General Commands Manual                                              COL(1)

NAME
     col - filter reverse line feeds from input

SYNOPSIS
     col [-bfpx] [-l num]

DESCRIPTION
     Col filters out reverse (and half reverse) line feeds so the output is in the correct order with only forward and half forward
     line feeds, and replaces white-space characters with tabs where possible.  This can be useful in processing the output of
     nroff(1) and tbl(1).

     Col reads from standard input and writes to standard output.

     The options are as follows:

     -b     Do not output any backspaces, printing only the last character written to each column position.

     -f     Forward half line feeds are permitted (‘‘fine’’ mode).  Normally characters printed on a half line boundary are printed
            on the following line.

     -p     Force unknown control sequences to be passed through unchanged.  Normally, col will filter out any control sequences
            from the input other than those recognized and interpreted by itself, which are listed below.

     -x     Output multiple spaces instead of tabs.

     -lnum  Buffer at least num lines in memory.  By default, 128 lines are buffered.

     The control sequences for carriage motion that col understands and their decimal values are listed in the following table:

     ESC-7            reverse line feed (escape then 7)
     ESC-8            half reverse line feed (escape then 8)
     ESC-9            half forward line feed (escape then 9)
     backspace        moves back one column (8); ignored in the first column
     carriage return  (13)
     newline          forward line feed (10); also does carriage return
     shift in         shift to normal character set (15)
     shift out        shift to alternate character set (14)
     space            moves forward one column (32)
     tab              moves forward to next tab stop (9)
     vertical tab     reverse line feed (11)

     All unrecognized control characters and escape sequences are discarded.

     Col keeps track of the character set as characters are read and makes sure the character set is correct when they are output.

     If the input attempts to back up to the last flushed line, col will display a warning message.

SEE ALSO
     expand(1), nroff(1), tbl(1)

STANDARDS
     The col utility conforms to the Single UNIX Specification, Version 2. The -l option is an extension to the standard.

HISTORY
     A col command appeared in Version 6 AT&T UNIX.

BSD                                                        June 17, 1991                                                        BSD
