# split

Linux下文件分割可以通过split命令来实现，而用cat进行文件合并。而分割可以指定按行数分割和安大小分割两种模式。

模式一：指定分割后文件行数
对与txt文本文件，可以通过指定分割后文件的行数来进行文件分割。
split -l 300 large_file.txt new_file_prefix

模式二：指定分割后文件大小
对于可执行文件等二进制文件，则不能通过文件行数来进行文件分割，此时我们可以指定分割大小来分隔文件。
split -b 10m large_file.bin new_file_prefix
对二进制文件我们同样也可以按文件大小来分隔。

在Linux下用cat进行文件合并：
命令：
cat small_files* > large_file
----------------------------------------------------------------------------------------------------
格式：split [-bl] file PREFIX
参数：
-b ：后面可接欲分割成的档案大小，可加单位，例如 b, k, m 等；
-l ：以行数来进行分割。


## split 实例

实例一：
/etc/termcap 有七百多K，若想要分成 300K 一个档案时？
# cd /tmp; split -b 300k /etc/termcap termcap
# ls -l termcap*
-rw-rw-r-- 1 root root 307200 8月 17 00:25 termcapaa
-rw-rw-r-- 1 root root 307200 8月 17 00:25 termcapab
-rw-rw-r-- 1 root root 184848 8月 17 00:25 termcapac

# 那个档名可以随意取的啦！我们只要写上前导文字，小档案就会以
# xxxaa, xxxab, xxxac 等方式来建立小档案的！

实例二：
如何将上面的三个小档案合成一个档案，档名为 termcapback
# cat termcap* >> termcapback
# 就用资料流重导向就好啦！


## man split

NAME
       split - split a file into pieces
SYNOPSIS
       split [OPTION] [INPUT [PREFIX]]
DESCRIPTION
       Output  fixed-size  pieces  of  INPUT  to PREFIXaa, PREFIXab, ...; default size is 1000 lines, and default PREFIX is `x'.
       With no INPUT, or when INPUT is -, read standard input.

       Mandatory arguments to long options are mandatory for short options too.
       -a, --suffix-length=N
              use suffixes of length N (default 2)
       -b, --bytes=SIZE
              put SIZE bytes per output file
       -C, --line-bytes=SIZE
              put at most SIZE bytes of lines per output file
       -d, --numeric-suffixes
              use numeric suffixes instead of alphabetic
       -l, --lines=NUMBER
              put NUMBER lines per output file
       --verbose
              print a diagnostic just before each output file is opened
       --help display this help and exit
       --version
              output version information and exit
       SIZE may have a  multiplier  suffix:  b  512,  kB  1000,  K  1024,  MB  1000*1000,  M  1024*1024,  GB  1000*1000*1000,  G
       1024*1024*1024, and so on for T, P, E, Z, Y.
