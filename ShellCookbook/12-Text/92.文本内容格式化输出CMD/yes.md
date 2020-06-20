# yes

用途说明
yes命令用于重复输出字符串（output a string repeatedly until killed）。这个命令可以帮你自动回答命令行提示。
例如：
进入一个含有多个文件的目录，执行 "yes | rm -i *"。
所有的 rm: remove regular empty file `xxx'? 提示都会被自动回答 y。这在编写脚本程序的时候会很用处。
yes命令还有另外一个用途，可以用来生成大的文本文件。
-----------------------------------------------------------------------------------------------------
常用参数
yes命令不指定参数时，不断的输出y；
指定字符串参数时，就不断的输出该字符串。
要终止输出，必须杀掉该进程，比如按Ctrl+C，或killall yes。
（Repeatedly output a line with all specified STRING(s), or ‘y’.）
比如：要不断输出n时，输入yes n。
-----------------------------------------------------------------------------------------------------
实例：
删除文件时自动回答y
# ls -l *.txt 
-rw-r--r-- 1 root root     7 11-28 11:54 1.txt
-rw-r--r-- 1 root root 10217 07-06 13:10 data.txt

# yes | rm -i *.txt 
---------------------------------------------------------
生成大的文本文件
下面的脚本把yes命令输出的内容保存到文件中，然后1秒钟之后停止输出。在这台测试机器上，生成了一个93M的文件。
#!/bin/sh    
yes hello >hello.txt &  
PID=$!    
sleep 1  
kill $PID   
ls -l hello.txt  
 
# ./yes.sh 
-rw-r--r-- 1 root root 93003776 11-28 14:02 hello.txt
./yes.sh: line 9:  5771 已终止                  yes hello > hello.txt
# ./yes.sh 
-rw-r--r-- 1 root root 95346688 11-28 14:09 hello.txt
./yes.sh: line 9:  7072 已终止                  yes hello > hello.txt
# ./yes.sh 
-rw-r--r-- 1 root root 0 11-28 14:09 hello.txt
# ./yes.sh 
-rw-r--r-- 1 root root 0 11-28 14:09 hello.txt
# ./yes.sh 
-rw-r--r-- 1 root root 0 11-28 14:09 hello.txt
# ./yes.sh 
-rw-r--r-- 1 root root 94040064 11-28 14:10 hello.txt
# ./yes.sh 
-rw-r--r-- 1 root root 0 11-28 14:10 hello.txt
# ./yes.sh 
-rw-r--r-- 1 root root 0 11-28 14:10 hello.txt

问题出现了：如果频繁的执行这个脚本，就会发现竟然生成0字节的文件，为何？

