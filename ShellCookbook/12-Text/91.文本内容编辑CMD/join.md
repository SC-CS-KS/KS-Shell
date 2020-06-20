# join

功能：
“将两个文件中指定栏位相同的行连接起来”，即按照两个文件中共同拥有的某一列，将对应的行拼接成一行。
 join [options] file1 file2
 注：这两个文件必须在已经在此列上是按照相同的规则进行了排序。
-------------------------------------------------------------------------------------------------
选项：
   -a FILENUM   除了显示匹配好的行，另外将指定序号（1或2）文件中不匹配的行显示出来
   -e EMPTY     将需要显示但是文件中不存在的域用此选项指定的字符代替
   -i           忽略大小写
   -j FIELD     等同于 -1 FIELD -2 FIELD,-j指定一个域作为匹配字段
   -o FORMAT    以指定格式输出
   -t CHAR      以指定字符作为输入输出的分隔符
                join 默认以空白字符做分隔符（空格和\t），可以使用 join -t $'\t'来指定使用tab做分隔符
   -v FILENUM   与-a相似 但只显示文件中没匹配上的行
   -1 FIELD     以file1中FIELD字段进行匹配
   -2 FIELD     以file2中FIELD字段进行匹配

# join 实例

文件 file1.txt
aa 1 2
bb 2 3
cc 4 6
dd 3 3

文件file2.txt
aa 2 1
bb 8 2
ff 2 4
cc 4 4
dd 5 5
-------------------------------------------------------------------------
01.join file1.txt file2.txt
 输出：aa 1 2 2 1
       bb 2 3 8 2
  默认以两个文件的第一行做匹配字段，默认以空格（不限个数）做分隔符。
-------------------------------------------------------------------------
02.join -j 1 file1.txt file2.txt
输出：aa 1 2 2 1
      bb 2 3 8 9
 -j选项 指定了以两个文件中第一列做匹配字段 等同于join file1.txt file2.txt
-------------------------------------------------------------------------
03. join -1 2 -2 3 file1.txt file2.txt
输出： 1 aa 2 aa 2
     2 bb 3 bb 8
     4 cc 6 ff 2 
     4 cc 6 cc 4
以第一个文件的第二列和第二个文件的第三列做匹配字段。
-------------------------------------------------------------------------
04 join -o 1.1 -o 1.2  -o 1.3 -o 2.1 -o 2.2 -o 2.3   -e 'empty' -a 1 file1.txt file2.txt 
输出： aa 1 2 aa 2 1
       bb 2 3 bb 8 2
       cc 4 6 empty empty empty
       dd 3 3 empty empty empty

 -o  指定将file1的1,2,3列，file2的1,2,3 列都输出。
 -a  指定将file1中不匹配的行也输出，但是file2中没有与file1后两行对应的字段，因此使用empty补齐。
-------------------------------------------------------------------------
05.join  -v 1  file1.txt file2.txt  
  输出： cc 4 6
         dd 3 3
 -v 1 只将file1中不匹配的行输出

PS：join命令和数据库中的join命令很类似。
    虽然file1和file2都已经排序，但是由于在第三行时开始不匹配因此只匹配上了前两行，后面的行虽然字段也可以对应但是已不能匹配。
    join命令，对文件格式的要求非常强，如果想要更灵活的使用，可用AWK命令。
-------------------------------------------------------------------------
06. join 标准输入
  有时我们需要将多个格式相同的文件join到一起，而join接受的是两个文件的指令，此时我们可以使用管道和字符“-"来实现
  join file1 file2 | join - file3 | join - file4  
  这样就可以将四个文件 连接到 一起了。

## man join

NAME
       join - join lines of two files on a common field

SYNOPSIS
       join [OPTION]... FILE1 FILE2

DESCRIPTION
       For  each pair of input lines with identical join fields, write a line to standard output.  The default join field is the
       first, delimited by whitespace.  When FILE1 or FILE2 (not both) is -, read standard input.

       -a FILENUM
              print unpairable lines coming from file FILENUM, where FILENUM is 1 or 2, corresponding to FILE1 or FILE2

       -e EMPTY
              replace missing input fields with EMPTY

       -i, --ignore-case
              ignore differences in case when comparing fields

       -j FIELD
              equivalent to `-1 FIELD -2 FIELD'

       -o FORMAT
              obey FORMAT while constructing output line

       -t CHAR
              use CHAR as input and output field separator

       -v FILENUM
              like -a FILENUM, but suppress joined output lines

       -1 FIELD
              join on this FIELD of file 1

       -2 FIELD
              join on this FIELD of file 2

       --check-order
              check that the input is correctly sorted, even if all input lines are pairable

       --nocheck-order
              do not check that the input is correctly sorted

       --help display this help and exit

       --version
              output version information and exit

       Unless -t CHAR is given, leading blanks separate fields and are ignored, else fields are separated by CHAR.  Any FIELD is
       a field number counted from 1.  FORMAT is one or more comma or blank separated specifications, each being `FILENUM.FIELD'
       or `0'.  Default FORMAT outputs the join field, the remaining fields from FILE1, the remaining  fields  from  FILE2,  all
       separated by CHAR.

       Important: FILE1 and FILE2 must be sorted on the join fields.  E.g., use `sort -k 1b,1' if `join' has no options.  If the
       input is not sorted and some lines cannot be joined, a warning message will be given.

