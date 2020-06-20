# dos2unix

unix2dos
perl -i -p -e 's/\n/\r\n/' file
sed -i -e 's/$/\r/' file

dos2unix
tr -d '\r' < file > file2
perl -i -p -e 's/\r//' file
sed -i -e 's/\r//' file
-------------------------------------------------------------------------------------
用途说明
dos2unix命令用来将DOS格式的文本文件转换成UNIX格式的（DOS/MAC to UNIX text file format converter）。
DOS下的文本文件是以\r\n作为断行标志的，表示成十六进制就是0D 0A。
而Unix下的文本文件是以\n作为断行标志的，表示成十六进制就是 0A。

DOS格式的文本文件在Linux底下，用较低版本的vi打开时行尾会显示^M，而且很多命令都无法很好的处理这种格式的文件，如果是个shell脚本。
而Unix格式的文本文件在Windows下用Notepad打开时会拼在一起显示。
因此产生了两种格式文件相互转换的需求，对应的将UNIX格式文本文件转成成DOS格式的是unix2dos命令。
-------------------------------------------------------------------------------------
常用参数
将DOS格式文本文件转换成Unix格式，最简单的用法就是dos2unix直接跟上文件名。
格式：dos2unix file

如果一次转换多个文件，把这些文件名直接跟在dos2unix之后。
（注：也可以加上-o参数，也可以不加，效果一样）
格式：dos2unix file1 file2 file3
格式：dos2unix -o file1 file2 file3

上面在转换时，都会直接在原来的文件上修改，如果想把转换的结果保存在别的文件，而源文件不变，则可以使用-n参数。
格式：dos2unix -n oldfile newfile

如果要保持文件时间戳不变，加上-k参数。所以上面几条命令都是可以加上-k参数来保持文件时间戳的。
格式：dos2unix -k file
格式：dos2unix -k file1 file2 file3
格式：dos2unix -k -o file1 file2 file3
格式：dos2unix -k -n oldfile newfile
注：unix2dos命令的使用方式与dos2unix命令的类似。
-------------------------------------------------------------------------------------

## dos2unix 实例

实例一
DOS格式的脚本文件时无法解释执行的，因为脚本文件的第一行是用来指定解释器的，Linux系统认为解释器是/bin/sh^M，而不是/bin/sh。
我们来通过Linux下的一些命令来看一下DOS格式文件的真面目。
# cat -v job.sh     //cat -v可以看到文件中的非打印字符，而不带-v参数的cat命令不行。
#!/bin/sh^M
^M
date >job.txt^M
^M

# hexdump -C job.sh       //hexdump -C可以看到文件每个字节的十六进制表示。
00000000  23 21 2f 62 69 6e 2f 73  68 0d 0a 0d 0a 64 61 74  |#!/bin/sh....dat|
00000010  65 20 3e 6a 6f 62 2e 74  78 74 0d 0a 0d 0a         |e >job.txt....|
0000001e

# vi job.sh     //使用vi打开时可以看到底下有[dos]的格式提示。有些版本vi显示的是行尾为^M。
#!/bin/sh
date >job.txt
~                                                                                                                                   
~              
"job.sh" [dos ] 4L, 30C
-------------------------------------------------------------------------------------
实例二 dos2unix -k和dos2unix -n的使用示例
# cat <<EOF >1.txt
> 1
> 2

> 3
> EOF

# file 1.txt
1.txt: ASCII text

# ls -l 1.txt
-rw-r--r-- 1 root root 6 11-14 09:08 1.txt

# date
2010年 11月 14日 星期日 09:28:42 CST

# unix2dos -k 1.txt      //保持文件时间戳
unix2dos: converting file 1.txt to DOS format ...

# ls -l 1.txt
-rw-r--r-- 1 root root 9 11-14 09:08 1.txt

# dos2unix -n 1.txt 2.txt     //将1.txt转换到2.txt
dos2unix: converting file 1.txt to file 2.txt in UNIX format ...
[root@jfht ~]# ls -l 1.txt 2.txt
-rw-r--r-- 1 root root 9 11-14 09:08 1.txt
-rw-r--r-- 1 root root 6 11-14 09:30 2.txt

# file 1.txt 2.txt
1.txt: ASCII text, with CRLF line terminators
2.txt: ASCII text

# cat -v 1.txt
1^M
2^M
3^M

# cat -v 2.txt
1
2
3
-------------------------------------------------------------------------------------

