# Shell 简介

Shell也叫做命令行界面，它是*nix操作系统下用户和计算机的交互界面。  
Shell这个词是指操作系统中提供访问内核服务的程序。  

先科普几个你可能不知道的事实：  

01.Shell几乎是和Unix操作系统一起诞生。
第一个Unix Shell是肯·汤普逊（Ken Thompson）以Multics上的Shell为模范在1971年改写而成，并命名Thompson sh。  
即便是后来流行的bash（shell的一种变体），它的年龄实际上比当前流行的所有的Linux kernel都大，  
可谓在Linux系统上是先有Shell再有Kernel。  

02.当前绝大部分*nix和MacOS操作系统里的默认的Shell都是bash，  
bash由Brian Fox在1987年创造，全称Bourne Again shell ( bash)。

03.你或许听说除了bash之外，还有Bourne shell ( sh)，Korn shell ( ksh)，C shell （包括 csh and tcsh）。
但是你知道这个星球上一共存在着大约50多种不同的shell么？  
想了解他们，请参考 http://www.freebsd.org/ports/shells.html。

04.每个月tiobe上都会给一个编程语言的排名，来显示各种语言的流行度。  

排名指数综合了全球范围内使用该语言的工程师人数、教学的课程数和第三方供应商数。
截止至2012年11月份，tiobe公布的编程语言排行榜里，bash的指数是0.56%排名22位。  
如果算上它旗下的awk 0.21%和tcl 0.146%，大概就能排到14名。  

注意这里还不包括bash的同源的兄弟姐妹csh、ksh等，算上它们，shell家族有望接近前十。
值得一提的是一直以来shell的排名就很稳定，不像某些“暴发户”语言。  
比如objective-c，这些语言的流行完全是因为当前Apple系的崛起，但这种热潮极有可能来得快去得更快。

05.全球最大的源代码仓库Github里，shell相关的项目数占到了8%，  
跻身前5和Java相当，可见在实战工程里，shell可谓宝刀不老。  

JavaScript	21%
Ruby	        12%
Java	        8%
Shell	        8%
Python	        8%
PHP	        7%
C	        6%
C++	        5%
Perl	        4%
CoffeeScript	3%
