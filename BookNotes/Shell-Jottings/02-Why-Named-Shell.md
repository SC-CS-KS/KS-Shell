# 为何叫做 Shell？

从技术角度来说，shell	是一个使用者与系统的互动界面(interface)，主要是让使用者透过命令行(command line)来使用系统以完成工作。
因此，Shell的最简单的定义就是---命令解译器(Command Interpreter)：
* 将使用者的命令翻译给核心处理，
* 同时，将核心处理结果翻译给使用者。

每次当我们完成系统登入，我们就取得一个互动模式的 shell，也称为login shell或 primary shell。  
若从进程(process)角度来说，我们在 shell所下达的命令，均是shell所产生的子进程。这现像，我们暂可称之为 fork。  

如果是执行脚本(shell script)的话，脚本中的命令则是由另外一个非互动模式的子shell (sub shell)来执行的。  
也就是primary shell产生sub shell的进程，sub shell再产生script中所有命令的行程。  

这裡，我们必须知道：kernel与  shell是不同的两套软体，而且都是可以被替换的：  
* 不同的作业系统使用不同的  kernel，  
* 而在同一个kernel之上，也可使用不同的 shell。  

在 linux的预设系统中，通常都可以找到好几种不同的shell，且通常会被列于如下文档里：  
/etc/shells   

不同的 shell有着不同的功能，且也彼此各异、或说"大同小异"。  

常见的shell主要分为两大主流：
sh：  
burne shell (sh)   
burne again shell (bash)   

csh：
c shell (csh)   
tc shell (tcsh)   
korn shell (ksh)   

大部份的  Linux	系统的预设shell都是bash，其原因大致如下两点：
* 自由软体  
* 功能强大  

bash是  gnu project最成功的产品之一，自推出以来深受广大 Unix用户喜爱，且也逐渐成为不少组织的系统标准。  
