# 04.SED 相关字符

## ! 感叹号

如果地址后面跟有感叹号（!），那么命令就应用于不匹配该地址的所有的行。
例如，要删除包含单词 "two" 的所有行，操作为：
```bash
$ sed '/two/ d' sample_one
one 1
three 1
one 1
three 1
```

## & 符号

01.如何将日志中的&callback=xxx&改为&callback=&
sed 's/&callback=xxx&/\&callback=\&' file
要注意在sed中&代表前面匹配模式串，如果要表示为&这个字符，需要在前面加\来引用。

## 元字符

元字符集
^           锚定行的开始                                如：/^sed/匹配所有以sed开头的行。   
$           锚定行的结束                                如：/sed$/匹配所有以sed结尾的行。   
.           匹配一个非换行符的字符                如：/s.d/匹配s后接一个任意字符，然后是d。 
*           匹配零或多个字符                          如：/*sed/匹配所有模板是一个或多个空格后紧跟sed的行。  
[]          匹配一个指定范围内的字符            如：/[Ss]ed/匹配sed和Sed。
[^]         匹配一个不在指定范围内的字符    如：/[^A-RT-Z]ed/匹配不包含A-R和T-Z的一个字母开头，紧跟ed的行。  
\(..\)      保存匹配的字符                              如：s/\(love\)able/\1rs，loveable被替换成lovers。保留匹配字符串的一部分  
&           保存搜索字符用来替换其他字符          如：s/love/**&**/，love这成**love**。保留匹配字符串的全部  
\<          锚定单词的开始                                  如：/\<love/匹配包含以love开头的单词的行。  
\>          锚定单词的结束                        如：/love\>/匹配包含以love结尾的单词的行。   
x\{m\}      重复字符x，m次                        如：/0\{5\}/匹配包含5个o的行。   
x\{m,\}     重复字符x,至少m次                     如：/o\{5,\}/匹配至少有5个o的行。   
x\{m,n\}    重复字符x，至少m次，不多于n次         如：/o\{5,10\}/匹配5--10个o的行。  
  
## 转义字符

首先是Sed里使用变量的问题，网上有人总结了四种方案：
1.       eval sed 's/$a/$b/' filename
2.       sed "s/$a/$b/" filename
3.       .sed 's/'$a'/'$b'/' filename
4.       .sed s/$a/$b/ filename
个人比较喜欢第二种，也就是：Sed后面的表达式一般用单引号引起来（'），当需要使用变量时就换用双引号（"）。

关于单双引号的区别：
单引号：shell处理命令时，对其中的内容不做任何处理，即此时是引号内的内容是sed命令所定义的格式。
双引号：shell处理命令时，要对其中的内容进行算术扩展。
             如果想让shell扩展后得到sed命令所要的格式，使用命令：sed -n "/\\\\$/p" haha,扩展后得到的结果即\\$.
 
因此对于语句类似：
$Comfilename="/home/evan/sandbox/Main/"
1. echo $Comfilename | sed 's#\/#\\\/#g'
2. echo $Comfilename | sed "s#\/#\\\/#g"
第一个的结果是：
\/home\/evan\/sandbox\/Main\/
第二个还是：
/home/evan/sandbox/Main/ 
因为双引号会将“\/“解释为”/“，所以sed "s#\/#\\\/#g"被Shell解释成了sed s#/#\/#g， 
到sed里执行时又把”\/“转义为”/“了，这样相当于进行了了两次解释，就得不到想要的结果了。

这个例子告诉我当没必要用双引号的的时候就不要用，要不然说不定什么时候你就会很郁闷。当然单引号效率要比双引号高也是不要滥用双引号的原因之一。
 
言归正传，如何在sed的变量里使用那些特殊的需要转义的字符呢？
网上提出的一种方法是将sed里表示替换用的s后面的表示分隔用的字符，换成别的变量里没有的字符这样就相当于变量里没有要转义的字符了。
如：
sed –i "s# $Comfilename#/Root/#" filename.list
这是一个好办法。
但很不幸我需要的是删除“d”不是替换“s“。
当我把同样的方法用于删除时似乎没有起到作用：sed –i "# $Comfilename#d" filename.list
于是只能自己手工先改写变量
$Comfilename="/home/evan/sandbox/Main/"
Tempname=` echo $Comfilename | sed 's#\/#\\\/#g'`  （这里把反单引号执行的结果给临时变量，同样的方法可以改写其他需要转义的符号。）
sed –i "# $ Tempname #d" filename.list


