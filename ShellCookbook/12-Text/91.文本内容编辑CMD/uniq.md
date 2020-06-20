# uniq

uniq [选项] 文件

说明：
这个命令读取输入文件，并比较相邻的行。
在正常情况下，第二个及以后更多个重复行将被删去。如果输入文件用“- ”表示，则从标准输入读取。
-------------------------------------------------------------------------------------------
选项：
– c 显示输出中，在每行行首加上本行在文件中出现的次数。
– d 只显示重复行。
– u 只显示文件中不重复的各行。

– n    前n个字段与每个字段前的空白一起被忽略。一个字段是一个非空格、非制表符的字符串，彼此由制表符和空格隔开(字段从0开始编号)。
+  n   前n个字符被忽略，之前的字符被跳过(字符从0开始编号)。
– f n 与- n相同，这里n是字段数。
– s n 与+n相同，这里n是字符数。
-------------------------------------------------------------------------------------------
注意：
这里的重复行与sort的-u选项有区别：
uniq过滤的是连续的重复行，如果相同的行之间存在其他不同行，不视为重复。
uniq命令不加任何参数，仅显示连续重复的行一次。

## uniq 实例

# uniq -c test2
2 boy took bat home1
1 girl took bat home2
2 boy took bat home3
3 dog brought hat home1
1
-c 参数显示文件中每行连续出现的次数。
---------------------------------------------------------------------------
# uniq -d testb
boy took bat homeb
boy took bat homed
dog brought hat home
-d选项仅显示文件中连续重复出现的行。
---------------------------------------------------------------------------
# uniq -u testg
girl took bat home
-u选项显示文件中没有连续出现的行。
---------------------------------------------------------------------------
# uniq -f 2 -s 2 testb
boy took bat home
忽略每行的前2个字段，忽略第二个空白字符和第三个字段的首字符，结果at home
---------------------------------------------------------------------------
# uniq -f 1 testb
boy took bat homed
dog brought hat home
忽略每行的第一个字段，这样boy ，girl开头的行看起来是连续重复的行。
---------------------------------------------------------------------------
# uniq -D testb
boy took bat homeb
boy took bat homeb
boy took bat homeb
boy took bat homed
dog brought hat homed
dog brought hat homed
dog brought hat home
显示所有重复的行，每个重复的行都显示

## man page

NAME
       uniq - report or omit repeated lines

SYNOPSIS
       uniq [OPTION]... [INPUT [OUTPUT]]

DESCRIPTION
       Discard(抛弃，丢弃) all but one of successive(连续的，继承的，接替的，依次的) identical(完全相同的) lines from INPUT (or standard input), writing to OUTPUT (or standard output).

       Mandatory arguments to long options are mandatory for short options too.

       -c, --count
              prefix lines by the number of occurrences

       -d, --repeated
              only print duplicate(n.副本，复制品 adj.复制的 vt.复制) lines

       -D, --all-repeated[=delimit-method]
              print all duplicate lines delimit-method={none(default),prepend,separate} Delimiting is done with blank lines.

       -f, --skip-fields=N
              avoid comparing the first N fields

       -i, --ignore-case
              ignore differences in case when comparing

       -s, --skip-chars=N
              avoid comparing the first N characters

       -u, --unique
              only print unique lines

       -z, --zero-terminated
              end lines with 0 byte, not newline

       -w, --check-chars=N
              compare no more than N characters in lines

       --help display this help and exit

       --version
              output version information and exit

       A field is a run of blanks (usually spaces and/or TABs), then non-blank characters.  Fields are skipped before chars.

       Note: 'uniq' does not detect repeated lines unless they are adjacent.  You may want to sort the input first, or use `sort
       -u' without `uniq'.
