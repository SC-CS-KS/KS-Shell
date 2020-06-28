# 01.SED 定址

可以通过定址来定位你所希望编辑的行，该地址用数字构成。

用逗号分隔的两个行数表示以这两行为起止的行的范围（包括行数表示的那两行）。
如1,3表示1,2,3行，美元符号($)表示最后一行。

范围可以通过数据，正则表达式或者二者结合的方式确定 。  

01.使用行号，可以是单独的行号，也可是一个行号的范围
$  表示最后一行
如 1,$就表示整个文本。

02.正则表达式
x            x为一个行号，如1
x,y          x到y的行号范围，如2，5，表示第二行到第五行
/pattern/    查询包含模式的行

以下部分是《LINUX与UNIX SHELL编程指南》上描述，但在Linux上实践不成功。
/pattern/pattern/      查询包含两个模式的行
/pattern/,x            在给定的行上查询包含模式的行
x,/pattern/            通过行号和模式查询匹配行
x,y                    查询x和y之外的行

## SED 地址和模式匹配（范围匹配） 

四种范围匹配模式：
（1）line1，line2；
（2）line1，/pattern1/；
（3）/pattern1/，line1；
（4）/pattern1/，/pattern2/；

1. 如果line或pattern匹配成功时，sed从line1匹配，直到line2或/pattern/
seq 6 |sed -n '1,3p'
1
2
3

2. 如果line，/pattern/匹配不成功时，又有下面几种情况：
<a> line=0时，只能使用0，/pattern/的模式，即从头匹配，直到pattern
seq 6 |sed -n '0,/3/p'
1
2
3
但下面的模式不能使用
seq 6 |sed -n '0,3p'
sed: -e expression #1, char 4: invalid usage of line address 0

关于0,/pattern/的原文解释：
A line number of 0 can be used in an address specification like 0,/regexp/ so that sed will try to match regexp in the first input line too. In other words, 0,/regexp/ is similar to 1,/regexp/, except that if addr2 matches the very first line of input the 0,/regexp/ form will consider it to end the range, whereas the 1,/regexp/ form will match the beginning of its range and hence make the range span up to the second occurrence of the regular expression.

<b> line 匹配成功，/pattern/匹配不成功，则是，从line行到最后一行
seq 6 |sed -n '3,/v/p'
3
4
5
6

<c> 就是下面例子中第二个line1，line2的特殊匹配情况，具体执行过程如下，
seq 6 |sed -e '1,2'd -e '1,2'd
读入第一行，1，2匹配成功，执行d，删除第一行，
读入第二行，1，2匹配成功，执行d，删除第一行
（这时，第一个-experssion执行完毕，执行第二个-experssion）
读入第三行，1，2匹配成功了一半，因为1匹配成功，这里可以像<a>中0一样，
"try to match regexp in the first input line too",所以执行d，删除第三行。
读入第四行，1，2匹配失败，第二个experssion执行结束，
读入第五行，第六行，结束。

为了更好的理解，结合前面的情况，执行下面代码：
1.      line1=2
2.      seq 6|sed -e '1,3d' -e"$line1,3d"
3.      5
4.      6
5.      #看来只要line1小于当前读入的行号就判读成功,你可以试试line1等于3的情况
6.      seq 6 |sed -e '1,2d' -e"$line1,/v/d'
7.      #没有输入，因为/v/匹配失败，所以命令d会一直执行到最后一行，把所有行都被删除。
8.      seq 6 |sed -e '1,2d' -e"$line1,5d'
9.      6
10.   #命令d只执行到第5行

总结：
（1）      line1，line2匹配模式下：
（A）line1不能为0；
（B）line1，line2匹配模式下，line1比现有的行号小，如在删除了1,2行之后，2,line2表示从现有开头匹配到line2，如果line2也不存在，则匹配第一行；

（2）      line1，/pattern1/匹配模式下：
（A）line1能为0；
（B）line1为0表示从开头匹配，直到匹配到/pattern1/，若没有匹配到/pattern1/，则直到输入结束；
（C）line1，/pattern1/匹配模式下，line1比现有的行号小，如在删除了1,2行之后，2,/pattern1/表示从现有开头匹配到/pattern1/；

（3）      /pattern1/，line1匹配模式下：
（A）         line1能为0；
（B）         line1比/pattern1/匹配的行号小，则只匹配到/pattern1/；
（C）         line1比/pattern1/匹配的行号大，从/pattern1/匹配到行line1；

（4）      /pattern1/，/pattern2/匹配模式下：
（A）         匹配到/pattern1/情况下，再继续匹配/pattern2/，若匹配不到/pattern2/则直到输入结束，否则匹配到/pattern2/；
（B）         未匹配到/pattern1/情况下，则无内容；

## SED 选定行的范围：逗号  

```bash
选定行的范围：逗号  
$ sed -n '/test/,/check/p' example
所有在模板test和check所确定的范围内的行都被打印。

#sed  -n '/sunny/,/adam/p' chan.t  
chansunny sunny chan
sunny adam 

$ sed -n '5,/^test/p' example
打印从第五行开始到第一个包含以test开始的行之间的所有行。

#sed  -n '1,/adam/p' chan.t  
chansunny sunny chan
sunny adam 
注意，如果未找到匹配行，就打印从1开始的后面所有行。

$ sed '/test/,/check/s/$/sed test/' example
对于模板test和west之间的行，每行的末尾用字符串sed test替换。

#sed   '/sunny/,/adam/s/$/piaoyu/' chan.t    
chansunny sunny chanpiaoyu
sunny adam piaoyu
chenguang chan chen
```
