# 15.SED 命令行实例


## 01.SED 引入shell变量

只有sed 引入shell变量，这样sed才能参与shell编程

单引号改双引号，‘/pattern/command’改'为"/pattern/command  " （单引号不支持扩展） 
变量名格式为${} 

```bash
#!/bin/bash
i=1
while [ $i -lt 10 ]
do
sed -n "${i}p" 111.txt
i=$(($i+1))
done
```

引用shell 变量也可以不加双引号，不加{ }，这样sed整个就不能带引号（双引号或单引号都不能带），这个变量必须是整个pattern。  

```bash
$ cat bb.sh
echo input pattern：
read pat
sed /$pat/d 111-tmp.txt   
```
 
## 10.SED 多行模式空间

在前面看到的都是单行模式。每次sed处理一个行。
但是sed是允许一次处理多行的。这就是所谓的多行模式空间。
多行模式空间命令有（N、D、P），他们分别对应单行模式空间（n、d、p）。
分别是他们的多行形式。只不过，d每次删除一行，而D每次删除多行模式空间中的“一行”，其实就是一个记录。
下面举例子。我们假设有一个文件，loves cc两个单词分散在不同的行。我们想通过sed将二者放在同一行。
[plain] view plaincopy
zoer@ubuntu:~$ cat data   
naughty loves  
cc,and cc loves naughty.  
they are very happy!  
zoer@ubuntu:~$ cat d  
N  
s/loves\ncc/loves cc\  
/  
zoer@ubuntu:~$ sed -f d data   
naughty loves cc  
,and cc loves naughty.  
they are very happy!  
例子中，使用N来创建多行模式空间，将loves\ncc直接替换成loves cc，然后在sed命令的末尾使用\来表示换行。则得到了上面的结果。

上面sed命令中的loves c\，反斜杠是换行之意。如果没有它，那么两行将成为一行。如下所示：

[plain] view plaincopy
zoer@ubuntu:~$ cat d  
N  
s/loves\ncc/loves cc\ /  
zoer@ubuntu:~$ sed -f d data   
naughty loves cc ,and cc loves naughty.  
they are very happy!  
---------------------------------------------------------
注意，sed命令中，也是可以使用{}的。但是与awk中的{}是不同的。
sed中的{}用法：
/条件/{符合条件的组合命令}      请注意这里的“条件”，只有符合条件的才会进入后面的组合命令。
意思是说，对符合前面条件的行，执行后面的组合命令。命令中可以包含一条或者多条命令。
而awk中的{}则分为BEGIN、END等作用。【不过个人认为，awk中的{}也有一部分的功能是组合一组命令】
-----------------------------------------------------
多行删除
假设我们有一个文件中有很多空白行，现在需要把多个相邻的空白行只保留一个。怎么做？
如果仅仅使用sed的d命令，那么这么写：
[plain] view plaincopy
zoer@ubuntu:~$ cat d  
/^$/{  
N  
/^\n$/d  
}  
[plain] view plaincopy
zoer@ubuntu:~$ cat data   
naughty loves  
  
cc,and cc loves naughty.  
  
  
they are very happy!  
  
  
here is any.  
  
and here.  

zoer@ubuntu:~$ sed -f d data   
naughty loves  
  
cc,and cc loves naughty.  
they are very happy!  
here is any.  
  
and here.  
我们会发现，偶数行连在一起的，都被删除了。而奇数行连在一起的会留下一行。这是为什么？

解释一下上面使用d命令的sed脚本就明白了。

上面的 sed脚本中，

读取一行，如果是 空行，那么符合条件，由于使用了N，所以会接着读取下一行。

如果此行是空行，那么符合^\n$，sed会由于d命令而删除整个模式空间，所以两个空行都被删除了。【同理如果有四个空行，那么可以看做是两倍的两个空行】

如果此行不是空行，那么不符合^\n$，sed会输出之前的空行和当前的这个非空行。

这就是为什么使用d的时候，偶数个连在一起的空白行被全部删了。而奇数个的还留有一行空白。

----------------------

如果使用D，世界瞬间安静了。

[plain] view plaincopy
zoer@ubuntu:~$ cat data   
naughty loves  
  
cc,and cc loves naughty.  
  
  
they are very happy!  
  
  
here is any.  
  
  
  
  
  
and here.  
zoer@ubuntu:~$ sed -f d data   
naughty loves  
  
cc,and cc loves naughty.  
  
they are very happy!  
  
here is any.  
  
and here.  
如果使用D，当读取到空行的时候，符合匹配的条件^$，然后会读取下一行，

1、如果不是空行，那么不匹配，输出之前的空行和刚刚读入的这个非空行。
2、如果是空行，那么匹配^\n$，此时删除上一个空行，将本次的空行作为输入继续运行这个脚本。  
显然，此时空行也是满足^$的，所以继续读取接下来的文件，重复1。


多行打印

我们通过下面的例子来学习多行打印的P命令。

[plain] view plaincopy
zoer@ubuntu:~$ cat d  
/unix$/{  
N  
/\nsystem/{  
s// operating &/  
P  
D  
}  
}  
zoer@ubuntu:~$ cat data  
here are examples of the unix  
system. where unix  
system appears,it should be the unix  
operating system.  
zoer@ubuntu:~$ sed -f d data   
here are examples of the unix operating   
system. where unix operating   
system appears,it should be the unix  
operating system.  
这里例子中，我们要匹配unix结尾的行，并且如果下一行是system，那么在unix之后加上operating。

注：在sed中，&意思是用正则表达式的内容替换掉读入的内容。另外，//之间不加任何内容，表示的是上一次的匹配。在这例子中就是\nsystem。

命令的执行过程如下：

首先读入一行。如果结尾匹配unix，那么读入下一行。

1、如果下一行以system开头，那么将system替换成 operating \nsystem，然后打印出来该替换之后的行并把原来的 行删除。

2、如果下一行不是system开头，那么以改行为输入，继续匹配是否以unix结尾。
