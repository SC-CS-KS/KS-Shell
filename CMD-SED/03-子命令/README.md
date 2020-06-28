# 03.SED 子命令

调用sed命令有两种形式：  
```bash
sed [options] 'command' file(s)  
sed [options] -f scriptfile file(s)
```

示例文件：
```bash
#cat who.t 
zxin10   pts/1        2013-04-16 13:55 (10.46.164.39)
zxin10   pts/3        2013-04-21 17:27 (10.46.164.39)  
```

* a\  
在当前行后面加入一行文本。
    
* i\  
在当前行上面插入文本。  
    
* c\  
用新的文本改变本行的文本。
```bash
#sed '1 c\chenguang'  who.t 
chenguang
zxin10   pts/3        2013-04-21 17:27 (10.46.164.39)
```

* d  
从模板块（Pattern space）位置删除行。 
```bash
#sed '1 d'  who.t               
zxin10   pts/3        2013-04-21 17:27 (10.46.164.39) 
```

* g  
获得内存缓冲区的内容，并替代当前模板块中的文本。   
```bash
#sed 's/zxin10/root/g' who.t 
root   pts/1        2013-04-16 13:55 (10.46.164.39)
root   pts/3        2013-04-21 17:27 (10.46.164.39)
```

* p  
打印模板块的行。  
```bash
#sed '1,2 p' who.t 
zxin10   pts/1        2013-04-16 13:55 (10.46.164.39)
zxin10   pts/1        2013-04-16 13:55 (10.46.164.39)
zxin10   pts/3        2013-04-21 17:27 (10.46.164.39)
zxin10   pts/3        2013-04-21 17:27 (10.46.164.39)
```    
* !  
表示后面的命令对所有没有被选定的行发生作用   
    
s/re/string   
用string替换正则表达式re   
    
* =  
打印当前行号码  
```bash
sed '/zxin10/=' who.t   
1
zxin10   pts/1        2013-04-16 13:55 (10.46.164.39)
2
zxin10   pts/3        2013-04-21 17:27 (10.46.164.39)
```

* D  
删除模板块的第一行。  
    
* b lable  
分支到脚本中带有标记的地方，如果分支不存在则分支到脚本的末尾。 
    
* h  
拷贝模板块的内容到内存中的缓冲区。 
    
* H  
追加模板块的内容到内存中的缓冲区  
    
* G  
获得内存缓冲区的内容，并追加到当前模板块文本的后面。 
    
* l  
列表不能打印字符的清单。显示与八进制ASCII代码等价的控制字符。  
  
* n  
读取下一个输入行，用下一个命令处理新的行而不是用第一个命令。  
    
* N  
追加下一个输入行到模板块后面并在二者间嵌入一个新行，改变当前行号码。 
    
* P（大写）  
打印模板块的第一行。  
    
* q  
第一个模式匹配完成后退出或立即退出。  
    
* r file  
从file中读行。
    
* t label  
if分支，从最后一行开始，条件一旦满足或者T，t命令，将导致分支到带有标号的命令处，或者到脚本的末尾。  
    
* T label  
错误分支，从最后一行开始，一旦发生错误或者T，t命令，将导致分支到带有标号的命令处，或者到脚本的末尾。  
    
* w file  
写并追加模板块到file末尾。  
    
* W file  
写并追加模板块的第一行到file末尾。 
    
* #  
把注释扩展到下一个换行符以前。  
  
    { } 
    在定位行执行的命令组  
      
s命令的替换标记
g表示行内全面替换。  
p表示打印行。  
w表示把行写入一个文件。 
x表示互换模板块中的文本和缓冲区中的文本。  
y表示把一个字符翻译为另外的字符（但是不用于正则表达式）  
    

## = 

[1addr]=
              Write the following to standard output:
              "%d\n", <current line number>

## a 命令 追加

a \
       text   Append text, which has each embedded newline preceded by a backslash.
```bash
#sed '1a\My name is sunny' chan.t 
My name is sunny'被追加到第一行后面，sed要求命令a后面有一个反斜杠。 
chansunny sunny chan
My name is sunny
sunny adam 
chenguang chan chen
```

## d/D 命令 删除

d      Delete pattern space.  Start next cycle.
D      Delete up to the first embedded newline in the pattern space.  Start next cycle, but skip reading from  the  input
              if there is still data in the pattern space.

d命令是删除当前模式空间内容（不在传至标准输出），并放弃之后的命令，并对新读取的内容，执行sed。  

```bash
$cat chen.t 
chenguang01
chenguang02
chenguang03

从文件中取出奇数行
$sed  'n;d' chen.t    
chenguang01
chenguang03

注释：
读取1行，执行n，得出2行，执行d，删除2行，得空，以此类推，读取3，因为n无法执行，所以d不执行。
因无-n参数，故输出1\n3。

#sed '2d' example
删除example文件的第二行。  

#sed '2,$d' example
删除example文件的第二行到末尾所有行。  //$表示最后一行，逗号表示范围

#sed '$d' example
删除example文件的最后一行。  

$ sed '/test/'d example
删除example文件所有包含test的行。  

D命令是删除当前模式空间开端至\n的内容（不在传至标准输出），放弃之后的命令，但是对剩余模式空间重新执行sed。

从文件中读取最后一行
$sed  'N;D' chen.t   
chenguang03
```

注释：  
读取1行，执行N，得出1行\n2行，执行D，得出2行，执行N，  
得出2行\n3行，执行D，得出3，执行N，条件失败退出，因无-n参数，故输出5行。  

## h/H g/G命令

       h H    Copy/append pattern space to hold space.
       g G    Copy/append hold space to pattern space.
---------------------------------------------------------------------------------------------
h命令是将当前模式空间中内容覆盖至保持空间，H命令是将当前模式空间中的内容追加至保持空间
g命令是将当前保持空间中内容覆盖至模式空间，G命令是将当前保持空间中的内容追加至模式空间
---------------------------------------------------------------------------------------------
将文件中数字和字母互换，并将字母大写
#cat ddd   
This is a and a is 1   
This is b and b is 2   
This is c and c is 3   
This is d and d is 4   
This is e and e is 5 

#cat ddd.sed
h  
{  
s/.*is \(.*\) and .*/\1/  
y/abcde/ABCDE/
G  
s/\(.*\)\n\(.*is \).*\(and \).*\(is \)\(.*\)/\2\5 \3\5 \4\1/  
}  
                                           
#sed -f ddd.sed ddd  
This is 1 and 1 is A  
This is 2 and 2 is B  
This is 3 and 3 is C  
This is 4 and 4 is D  
This is 5 and 5 is E

注释：读取1行，执行h，复制到保持空间，执行s，模式空间得到匹配到的字母a，然后执行y，将a转成A，执行G，追加保持空间内容到模式空间，得
A\nThis is a and a is 1；执行s，重新排列，得出This is 1 and 1 is A；以此类推，得出结果。
这里需要注意的是匹配的内容中，空格一定要处理好，空格处理不对，会造成第二次s匹配错误，无法执行重新排列或排列错误。
------------------------------------------------------------------------------------------------------------------
$ sed -e '/test/h' -e '$G example
在sed处理文件的时候，每一行都被保存在一个叫模式空间的临时缓冲区中，除非行被删除或者输出被取消，否则所有被处理的行都将打印在屏幕上。
接着模式空间被清空，并存入新的一行等待处理。

在这个例子里，匹配test的行被找到后，将存入模式空间，h命令将其复制并存入一个称为保持缓存区的特殊缓冲区内。
第二条语句的意思是，当到达最后一行后，G命令取出保持缓冲区的行，然后把它放回模式空间中，且追加到现在已经存在于模式空间中的行的末尾。
在这个例子中就是追加到最后一行。简单来说，任何包含test的行都被复制并追加到该文件的末尾。 
------------------------------------------------------------------------------------------------------------------

## i 命令 插入

 i \
       text   Insert text, which has each embedded newline preceded by a backslash.
-----------------------------------------------------------------------------------------------------------------
#sed '1 i\My name is sunny' chan.t  

My name is sunny
chansunny sunny chan
sunny adam 
chenguang chan chen
-----------------------------------------------------------------------------------------------------------------


## n/N 命令

       n N    Read/append the next line of input into the pattern space.
------------------------------------------------------------------------------------------
n命令简单来说就是提前读取下一行，覆盖模型空间前一行（并没有删除，因此依然打印至标准输出）。
如果命令未执行成功（并非跳过：前端条件不匹配），则放弃之后的任何命令，并对新读取的内容，重头执行sed。

$cat chen.t 
chenguang01
chenguang02
chenguang03

例子：从文件中取出偶数行
$sed -n '/chen/{n;p}'  chen.t 
chenguang02

注释：
读取第一行，执行n命令，此时模式空间为第二行，执行p，打印模式空间内容第二行，之后读取第三行，执行n命令。
之后读取第四行，因为没有了，所以退出，并放弃p命令。
因此，最终打印出来的就是偶数行。
------------------------------------------------------------------------------------------
$ sed '/test/{ n; s/aa/bb/; }' example
如果test被匹配，则移动到匹配行的下一行，替换这一行的aa，变为bb，并打印该行，然后继续。

#sed '/chan/{n;s/adam/suuunny/;}' chan.t 
chansunny sunny chan
sunny suuunny 
chenguang chan chen 

#sed '/adm/{n;s/adam/suuunny/;}' chan.t     
chansunny sunny chan
sunny adam 
chenguang chan chen
-----------------------------------------------------------------------------------------------------------------------------------------------
N命令简单来说就是追加下一行到模式空间，同时将两行看做一行，但是两行之间依然含有\n换行符。
如果命令未执行成功（并非跳过：前端条件不匹配），则放弃之后任何命令，并对新读取的内容，重头执行sed。
例子：从文件中读取奇数行
$sed -n '$! N;P' chen.t   
chenguang01
chenguang03

注释：读取第一行，$!条件满足（不是尾行），执行N命令，得出第一行\n第二行，执行P，打印得第一行。
读取第三行$!条件不满足，跳过N，执行P，打印得第三行。


## p/P 命令

       p      Print the current pattern space.
       P      Print up to the first embedded newline of the current pattern space.
------------------------------------------------------------------------------------------------------
p打印当前模式空间内容，追加到默认输出之后。
P打印当前模式空间开端至\n的内容，并追加到默认输出之前。

$cat chen.t 
chenguang01
chenguang02
chenguang03

$sed '/chen/p'  chen.t 
chenguang01
chenguang01
chenguang02
chenguang02
chenguang03
chenguang03
------------------------------------------------------------------------------------------------------
sed并不对每行末尾\n进行处理，但是对N命令追加的行间\n进行处理，因为此时sed将两行看做一行。
$sed '/chen/{N;P}'  chen.t 
chenguang01
chenguang01
chenguang02
chenguang03

$sed '/chen/{N;p}'  chen.t  
chenguang01
chenguang02
chenguang01
chenguang02
chenguang03

#当前模式空间中内容是
chenguang01
chenguang02

P命令只打印第一行，因为最后是换行符
p打印全部

## q 命令 退出

q      Immediately  quit  the sed script without processing any more input, except that if auto-print is not disabled the
              current pattern space will be printed.
---------------------------------------------------
#sed '10q' example
打印完第10行后，退出sed。即打印前10行。
---------------------------------------------------
#sed -n '1,10p' tmp.c 
和上面的命令效果一样。
---------------------------------------------------

## r 命令 从文件读入  

       r filename
              Append text read from filename.
--------------------------------------------------------
$ sed '/test/r file' example
file里的内容被读进来，显示在与test匹配的行后面。
如果匹配多行，则file的内容将显示在所有匹配行的下面。

#cat num.t 
123456
789123

#sed  '/chan/r num.t'  chan.t    
chansunny sunny chan
123456
789123
sunny adam 
chenguang chan chen
123456
789123
--------------------------------------------------------

## s 命令 替换

       s/regexp/replacement/
              Attempt  to match regexp against the pattern space.  If successful, replace that portion matched with replacement.
              The replacement may contain the special character & to refer to that portion of the pattern space  which  matched,
              and the special escapes \1 through \9 to refer to the corresponding matching sub-expressions in the regexp.
------------------------------------------------------------------------------
替换：s命令
示例文件：  
#cat chan.t 
chansunny sunny chan
sunny adam 
chenguang chan chen
------------------------------------------------------------------------------
$ sed 's/test/mytest/g' example
在整行范围内把test替换为mytest。如果没有g标记，则只有每行第一个匹配的test被替换成mytest。  
#sed 's/chan/chen/' chan.t 
chensunny sunny chan
sunny adam 
chenguang chen chen

#sed 's/chan/chen/g' chan.t 
chensunny sunny chen
sunny adam 
chenguang chen chen
------------------------------------------------------------------------------
$ sed -n 's/^test/mytest/p' example
(-n)选项和p标志一起使用表示只打印那些发生替换的行。也就是说，如果某一行开头的test被替换成mytest，就打印它。
#sed -n 's/chan/chen/gp' chan.t 
chensunny sunny chen
chenguang chen chen
------------------------------------------------------------------------------
$ sed 's/^192.168.0.1/&localhost/' example
    &符号表示替换字符串中被找到的部份。所有以192.168.0.1开头的行都会被替换成它自已加 localhost，变成192.168.0.1localhost。        
#sed  's/chan/&guang/g' chan.t          
changuangsunny sunny changuang
sunny adam 
chenguang changuang chen
------------------------------------------------------------------------------
$ sed -n 's/\(love\)able/\1rs/p' example
love被标记为1，所有loveable会被替换成lovers，而且替换的行会被打印出来。
#sed  's/\(chan\)sunny/\1guang/' chan.t  
changuang sunny chan
sunny adam 
chenguang chan chen  
------------------------------------------------------------------------------
$ sed 's#10#100#g' example
不论什么字符，紧跟着s命令的都被认为是新的分隔符，所以，“#”在这里是分隔符，代替了默认的“/”分隔符。表示把所有10替换成100。 
#sed  's#\(chan\)sunny#\1guang#' chan.t  
changuang sunny chan
sunny adam 
chenguang chan chen
------------------------------------------------------------------------------

## w 命令 写入文件 

       w filename
              Write the current pattern space to filename.
       W filename
              Write the first line of the current pattern space to filename.
-----------------------------------------------------------------
$ sed -n '/test/w file' example
在example中所有包含test的行都被写入file里。 

#sed -n  '/chan/w num.t'  chan.t
#cat num.t 
chansunny sunny chan
chenguang chan chen
注：替换了num.t文件的内容
-----------------------------------------------------------------

## x 命令 互换

x      Exchange the contents of the hold and pattern spaces.

```bash
# sed -e '/test/h' -e '/check/x' example 
互换模式空间和保持缓冲区的内容。也就是把包含test与check的行互换。
```

注：
把/test/模式匹配行，保存至模式空间，再用h命令复制到保持缓冲区。
读入/check/匹配行至模式空间，用保持缓冲区中的内容替换模式空间的内容。

## y 命令 变形

       y/source/dest/
              Transliterate the characters in the pattern space which appear in source to the corresponding character in dest.
```bash
$ sed '1,10y/abcde/ABCDE/' filename
# 把1--10行内所有abcde转变为大写。
# 注意，正则表达式元字符不能使用这个命令。

# 如果需要对某个字符串进行大小写转换，则可使用如下方法：
sed 's/\b[a-z]\b/\u&/g'  filename
```
