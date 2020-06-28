# SED
 
sed 是一种流式编辑器，它一次处理一行内容。
处理时，把当前处理的行存储在临时缓冲区中，称为“模式空间”（pattern space）。
接着用sed命令处理缓冲区中的内容，处理完成后，把缓冲区的内容送往屏幕。
接着处理下一行，这样不断重复，直到文件末尾。文件内容并没有改变，除非你使用重定向存储输出。
Sed主要用来自动编辑一个或多个文件，简化对文件的反复操作，编写转换程序等。

Sed脚本
Sed脚本是一个sed的命令清单，启动Sed时以-f选项引导脚本文件名。
Sed对于脚本中输入的命令非常挑剔，在内部命令的末尾不能有任何空白或文本。如果在一行中有多个命令，要用分号分隔。
以#开头的行为注释行，且不能跨行。

sed执行模板=sed '模式{命令1;命令2}'
即逐行读入模式空间，执行命令，最后输出打印出来。
如果执行命令1失败，跳过命令2，读入下一行继续执行。

## man page  

NAME
       sed - stream editor for filtering and transforming text
SYNOPSIS
       sed [OPTION]... {script-only-if-no-other-script} [input-file]...
DESCRIPTION
       Sed  is  a  stream  editor.   A stream editor is used to perform basic text transformations on an input stream (a file or
       input from a pipeline).  While in some ways similar to an editor which permits scripted edits (such as ed), sed works  by
       making  only one pass over the input(s), and is consequently more efficient.  But it is sed's ability to filter text in a
       pipeline which particularly distinguishes it from other types of editors.  
