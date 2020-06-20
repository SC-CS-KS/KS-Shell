# tr

可以将 tr 看作为 sed 的（极其）简化的变体：
tr主要用于删除文件中控制字符或进行字符转换。
它可以用一个字符来替换另一个字符，或者可以完全除去一些字符，也可以用它来除去重复字符。

注意：
tr是从标准输入读入，文件不作为参数。所以只能通过管道，或者输入重定向符获取处理文本内容。
很明显，如果是用文件作为标准输入，源文件的内容是不会改变的，可以通过输出重定向到另外一个文件来保存修改后的内容。
-----------------------------------------------------------------------------------------------------------
使用tr时要转换两个字符串：
字符串1用于查询，字符串2用于处理各种转换。
tr刚执行时，字符串1中的字符被映射到字符串2中的字符，然后转换操作开始。
-----------------------------------------------------------------------------------------------------------
带有最常用选项的tr命令格式为：
tr -c -d -s ["string1_to_translate_from"] ["string2_to_translate_to"] < input-file

-c 用字符串1中字符集的补集替换此字符集，要求字符集为ASCII。
-d 删除字符串1中所有输入字符。
-s 删除所有重复出现字符序列，只保留第一个。即将重复出现字符串压缩为一个字符串。
-----------------------------------------------------------------------------------------------------------
字符范围
指定字符串1或字符串2的内容时，只能使用单字符或字符串范围或列表。

[a-z] a-z内的字符组成的字符串。
[A-Z] A-Z内的字符组成的字符串。
[0-9] 数字串。

\octal 一个三位的八进制数，对应有效的ASCII字符。
[O*n] 表示字符O重复出现指定次数n，因此[O*2]匹配OO的字符串。
-----------------------------------------------------------------------------------------------------------
tr中特定控制字符的不同表达方式，速记符含义八进制方式
\a Ctrl-G  铃声\007
\b Ctrl-H  退格符\010
\f Ctrl-L  走行换页\014
\n Ctrl-J  新行\012
\r Ctrl-M  回车\015
\t Ctrl-I  tab键\011
\v Ctrl-X  \030

## tr 实例

实例：
1、将文件file中出现的"abc"替换为"xyz"
# cat file | tr "abc" "xyz" > new_file

【注意】这里，凡是在file中出现的"a"字母，都替换成"x"字母，"b"字母替换为"y"字母，"c"字母替换为"z"字母。
而不是将字符串"abc"替换为字符串"xyz"。
------------------------------------------------------------
2、使用tr命令“统一”字母大小写
（小写 --> 大写）
# cat file | tr [a-z] [A-Z] > new_file

（大写 --> 小写）
# cat file | tr [A-Z] [a-z] > new_file
------------------------------------------------------------
3、把文件中的数字0-9替换为a-j
# cat file | tr [0-9] [a-j] > new_file
------------------------------------------------------------
4、删除文件file中出现的"Snail"字符
# cat file | tr -d "Snail" > new_file

【注意】这里，凡是在file文件中出现的'S','n','a','i','l'字符都会被删除！而不是紧紧删除出现的"Snail”字符串。
------------------------------------------------------------
5、删除文件file中出现的换行'\n'、制表'\t'字符
# cat file | tr -d "\n\t" > new_file

不可见字符都得用转义字符来表示的，这个都是统一的。
------------------------------------------------------------
6、删除“连续着的”重复字母，只保留第一个
# cat file | tr -s [a-zA-Z] > new_file
------------------------------------------------------------
7、删除空行
# cat file | tr -s "\n" > new_file
------------------------------------------------------------
8、删除Windows文件“造成”的'^M'字符
# cat file | tr -d "\r" > new_file
或者
# cat file | tr -s "\r" "\n" > new_file
 
【注意】这里-s后面是两个参数"\r"和"\n"，用后者替换前者
------------------------------------------------------------
9、用空格符\040替换制表符\011
# cat file | tr -s "\011" "\040" > new_file
------------------------------------------------------------
10、把路径变量中的冒号":" 替换成换行符"\n"
# echo $PATH | tr -s ":" "\n"

## Man Page

NAME
       tr - translate or delete characters

SYNOPSIS
       tr [OPTION]... SET1 [SET2]

DESCRIPTION
       Translate, squeeze（压缩）, and/or delete characters from standard input, writing to standard output.

       -c, -C, --complement
              first complement SET1
       
       （取SET1的反义，tr要替换的字符，变成未列在SET1中的字符，此选项通常和s、d选项配合使用。
         -c处理的是二进制字节值，-C处理是的现行locale所定义的字符。直到2005年初，仍有许多系统不支持-C选项）
       -d, --delete
              delete characters in SET1, do not translate

       -s, --squeeze-repeats
              replace  each input sequence of a repeated character that is listed in SET1 with a single occurrence of that char-
              acter

       -t, --truncate-set1
              first truncate（截断） SET1 to length of SET2

       --help display this help and exit

       --version
              output version information and exit

       SETs are specified as strings of characters.  Most represent themselves.  Interpreted sequences are:

       \NNN   character with octal value NNN (1 to 3 octal digits)

       \\     backslash

       \a     audible BEL

       \b     backspace

       \f     form feed

       \n     new line

       \r     return

       \t     horizontal tab

       \v     vertical tab

       CHAR1-CHAR2
              all characters from CHAR1 to CHAR2 in ascending order

       [CHAR*]
              in SET2, copies of CHAR until length of SET1

       [CHAR*REPEAT]
              REPEAT copies of CHAR, REPEAT octal if starting with 0

       [:alnum:]
              all letters and digits

       [:alpha:]
              all letters

       [:blank:]
              all horizontal whitespace

       [:cntrl:]
              all control characters

       [:digit:]
              all digits

       [:graph:]
              all printable characters, not including space

       [:lower:]
              all lower case letters

       [:print:]
              all printable characters, including space

       [:punct:]
              all punctuation characters

       [:space:]
              all horizontal or vertical whitespace

       [:upper:]
              all upper case letters

       [:xdigit:]
              all hexadecimal digits

       [=CHAR=]
              all characters which are equivalent to CHAR

       Translation occurs if -d is not given and both SET1 and SET2 appear.  -t may be used  only  when  translating.   SET2  is
       extended  to  length  of SET1 by repeating its last character as necessary.  Excess characters of SET2 are ignored.  Only
       [:lower:] and [:upper:] are guaranteed to expand in ascending order; used in SET2 while translating,  they  may  only  be
       used  in  pairs  to  specify case conversion.  -s uses SET1 if not translating nor deleting; else squeezing uses SET2 and
       occurs after translation or deletion.

AUTHOR
       Written by Jim Meyering.

REPORTING BUGS
       Report bugs to <bug-coreutils@gnu.org>.

COPYRIGHT
       Copyright   (C)   2008   Free   Software   Foundation,   Inc.    License   GPLv3+:   GNU   GPL   version   3   or   later
       <http://gnu.org/licenses/gpl.html>
       This is free software: you are free to change and redistribute it.  There is NO WARRANTY, to the extent permitted by law.

SEE ALSO
       The full documentation for tr is maintained as a Texinfo manual.  If the info and tr programs are properly  installed  at
       your site, the command

              info coreutils 'tr invocation'

       should give you access to the complete manual.


