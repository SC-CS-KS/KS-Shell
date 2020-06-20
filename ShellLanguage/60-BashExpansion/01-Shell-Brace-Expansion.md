# 01.Shell Brace Expansion

是一种可能产生任意字符串的机制。

花括号扩展的模式是一个可选的 preamble (前导字符),  后面跟着一系列逗号分隔的字符串，包含在一对花括号中，再后面是一个可选的postscript(附言)。  
前导被添加到花括号中的每个字符串前面，附言被附加到每个结果字符串之后， 从左到右进行扩展。  

花括号扩展可以嵌套。 

扩展字符串的结果没有排序，而是保留了从左到右的顺序。   
例如， a{d,c,b}e 扩展为 `ade ace abe'。   

花括号扩展是在任何其他扩展之前进行的，任何对其他扩展有特殊意义的字符都保留在结果中。它是严格字面上的。     

Bash不会对扩展的上下文或花括号中的文本做任何语义上的解释。  
正确的花括号扩展必须包含没有引用的左括号和右括号，以及至少一个没有引用的逗号。任何不正确的表达式都不会被改变。
可以用反斜杠来引用 { 或 ,  来阻止将它们识别为花括号表达式的一部分。 为了避免与参数扩展冲突，字符串 ${ 不被认为有效的组合。  

这种结构通常用来简写字符串的公共前缀远比上例中为长的情况，例如：  

mkdir /usr/local/src/bash/{old,new,dist,bugs}  
或者
chown root /usr/{ucb/{ex,edit},lib/{ex?.?*,how_ex}}    

花括号扩展导致了与历史版本的 sh 的一点不兼容。  
在左括号或右括号作为词的一部分出现时， sh  不会对它们进行特殊处理，会在输出中保留它们。  

Bash 将括号从花括号扩展结果的词中删除。
例如，向 sh 输入 file{1,2} 会导致不变的输出。
同样的输入在 bash 进行扩展之后，会输出 file1 file2   
如果需要同 sh 严格地保持兼容，需要在启动 bash 的时候使用 +B  选项，或者使用  set 命令加上 +B 选项来禁用花括号扩展。  
(参见下面的 Shell 内建命令(SHELL BUILTIN COMMANDS) 章节)。 

* 示例1：操作多个文件名有共同点的文件 
$ ls
bk
$ touch file{source,target,info,readme}.txt
$ ls
bk  fileinfo.txt  filereadme.txt  filesource.txt  filetarget.txt

$ echo file{1,2,3,4}.txt
file1.txt file2.txt file3.txt file4.txt
$ touch file{1,2,3,4}.txt
$ ls
bk  file1.txt  file2.txt  file3.txt  file4.txt

示例2：一个命令将几个文件名有规律的文件转移到一个目录
$ls bk
$ mv file{1,2,3,4}.txt bk/.
$ ls bk
file1.txt  file2.txt  file3.txt  file4.txt

示例3：备份
$touch mydata.log
$ vi back.sh
$ chmod +x back.sh 
$ cat back.sh 
set -x #调试模式
bkdate=`date +%F` #日期
cp mydata{,$bkdate}.log #将log备份为带日期格式

$ls
back.sh  bk  mydata.log
$ ./back.sh 
+++ date +%F
++ bkdate=2010-06-10
++ cp mydata.log mydata2010-06-10.log
$ ls
back.sh  bk  mydata2010-06-10.log  mydata.log

示例4 处理多个有规律文件名
$ echo /var/log/messages.{1..3}
/var/log/messages.1 /var/log/messages.2 /var/log/messages.3

$ echo file{a..d}{1..3}.txt
filea1.txt filea2.txt filea3.txt fileb1.txt fileb2.txt fileb3.txt filec1.txt filec2.txt filec3.txt filed1.txt filed2.txt filed3.txt

示例5 for循环
$ for i in {1..9..3}
> do
> echo $i
> done
1
4
7

## Brace Expansion
       Brace expansion is a mechanism by which arbitrary strings may be generated.  This mechanism is similar to pathname expansion, but the file-
       names generated need not exist.  Patterns to be brace expanded take the form of an optional preamble, followed by either a series of comma-
       separated  strings or a sequence expression between a pair of braces, followed by an optional postscript.  The preamble is prefixed to each
       string contained within the braces, and the postscript is then appended to each resulting string, expanding left to right.

       Brace expansions may be nested.  The results of each expanded string are not sorted; left  to  right  order  is  preserved.   For  example,
       a{d,c,b}e expands into 'ade ace abe'.

       A  sequence  expression  takes  the  form  {x..y}, where x and y are either integers or single characters.  When integers are supplied, the
       expression expands to each number between x and y, inclusive.  When characters are supplied, the expression expands to each character lexi-
       cographically between x and y, inclusive.  Note that both x and y must be of the same type.

       Brace  expansion  is performed before any other expansions, and any characters special to other expansions are preserved in the result.  It
       is strictly textual.  Bash does not apply any syntactic interpretation to the context of the expansion or the text between the braces.

       A correctly-formed brace expansion must contain unquoted opening and closing braces, and at least one unquoted comma or  a  valid  sequence
       expression.   Any  incorrectly formed brace expansion is left unchanged.  A { or , may be quoted with a backslash to prevent its being con-
       sidered part of a brace expression.  To avoid conflicts with parameter expansion, the string ${ is not considered eligible for brace expan-
       sion.

       This construct is typically used as shorthand when the common prefix of the strings to be generated is longer than in the above example:

              mkdir /usr/local/src/bash/{old,new,dist,bugs}
       or
              chown root /usr/{ucb/{ex,edit},lib/{ex?.?*,how_ex}}

       Brace  expansion introduces a slight incompatibility with historical versions of sh.  sh does not treat opening or closing braces specially
       when they appear as part of a word, and preserves them in the output.  Bash removes braces from words as a consequence of brace  expansion.
       For  example,  a word entered to sh as file{1,2} appears identically in the output.  The same word is output as file1 file2 after expansion
       by bash.  If strict compatibility with sh is desired, start bash with the +B option or disable brace expansion with the +B  option  to  the
       set command (see SHELL BUILTIN COMMANDS below).

