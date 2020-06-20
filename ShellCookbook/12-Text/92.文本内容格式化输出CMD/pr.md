# pr

pr-将文本文件转换为打印文件

基于字符的打印机使用等宽字体使得每页有固定数目的行并且每行有固定的字符数。
pr则用于调整文本以适应特定纸张的大小，并且可自主选择页眉和页边距。
-----------------------------------------------------------------------------------------
NAME
       pr - convert text files for printing

SYNOPSIS
       pr [OPTION]... [FILE]...

DESCRIPTION
       Paginate(为...标页数) or columnate FILE(s) for printing.

## man pr

pr通常用于管道传输的过滤器。
下面例子生成了一个/usr/bin文件夹下的目录列表，并用pr将其格式化为分页的、三列的输出。
$  ls /usr/bin | pr -3 -w 65 | head

　　2012-02-18 14:00                                                        Page 1

　　[                               apturl                  bsd-write

　　411toppm                        ar                          bsh

　　a2p                             arecord                 btcflash

　　a2ps                            arecordmidi             bug-buddy

　　a2ps-lpr-wrapper                ark                         buildhash

