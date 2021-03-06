# 20.SED MISC

使用SED：
Sed接受一个或多个编辑命令，并且每读入一行后就依次应用这些命令。当读入第一行输入后，sed对其应用所有的命令，然后将结果输出。
接着再读入第二行输入，对其应用所有的命令……并重复这个过程。上一个例子中sed由标准输入设备（即命令解释器，通常是以管道输入的形式）获得输入。
在命令行给出一个或多个文件名作为参数时，这些文件取代标准输入设备成为sed的输入。sed的输出将被送到标准输出（显示器）。
因此：
cat filename | sed '10q'         # 使用管道输入
sed '10q' filename               # 同样效果，但不使用管道输入
sed '10q' filename > newfile     # 将输出转移（重定向）到磁盘上

要了解sed命令的使用说明，包括如何通过脚本文件（而非从命令行）来使用这些命令，请参阅《sed & awk》第二版，作者Dale Dougherty和Arnold Robbins
（O'Reilly，1997；http://www.ora.com），《UNIX Text Processing》，作者Dale Dougherty和Tim O'Reilly（Hayden Books，1987）或者是Mike Arst写的教程——压缩包的名称是“U-SEDIT2.ZIP”（在许多站点上都找得到）。要发掘sed的潜力，则必须对“正则表达式”有足够的理解。正则表达式的资料可以看《Mastering Regular Expressions》作者Jeffrey Friedl（O'reilly 1997）。Unix系统所提供的手册页（“man”）也会有所帮助（试一下这些命令“man sed”、“man regexp”，或者看“man ed”中关于正则表达式的部分），但手册提供的信息比较“抽象”——这也是它一直为人所诟病的。不过，它本来就不
是用来教初学者如何使用sed或正则表达式的教材，而只是为那些熟悉这些工具的人提供的一些文本参考。

括号语法：
前面的例子对sed命令基本上都使用单引号（'...'）而非双引号（"..."）这是因为sed通常是在Unix平台上使用。
单引号下，Unix的shell（命令解释器）不会对美元符（$）和后引号（`...`）进行解释和执行。
而在双引号下美元符会被展开为变量或参数的值，后引号中的命令被执行并以输出的结果代替后引号中的内容。
而在“csh”及其衍生的shell中使用感叹号（!）时需要在其前面加上转义用的反斜杠（就像这样：\!）以保证上面所使用的例子能正常运行
（包括使用单引号的情况下）。DOS版本的Sed则一律使用双引号（"..."）而不是引号来圈起命令。

'\t'的用法：
为了使本文保持行文简洁，我们在脚本中使用'\t'来表示一个制表符。
但是现在大部分版本的sed还不能识别'\t'的简写方式，因此当在命令行中为脚本输入制表符时，你应该直接按TAB键来输入制表符而不是输入'\t'。
下列的工具软件都支持'\t'做为一个正则表达式的字元来表示制表符：awk、perl、HHsed、
sedmod以及GNU sed v3.02.80。

不同版本的SED：
不同的版本间的sed会有些不同之处，可以想象它们之间在语法上会有差异。
具体而言，它们中大部分不支持在编辑命令中间使用标签（:name）或分支命令（b,t），除非是放在那些的末尾。
这篇文档中我们尽量选用了可移植性较高的语法，以使大多数版本的sed的用户都能使用这些脚本。
不过GNU版本的sed允许使用更简洁的语法。想像一下当读者看到一个很长的命令时的心情：
sed -e '/AAA/b' -e '/BBB/b' -e '/CCC/b' -e d

好消息是GNU sed能让命令更紧凑：
   sed '/AAA/b;/BBB/b;/CCC/b;d'      # 甚至可以写成
   sed '/AAA\|BBB\|CCC/b;d'
此外，请注意虽然许多版本的sed接受象“/one/ s/RE1/RE2/”这种在's'前带有空格的命令，但这些版本中有些却不接受这样的命令:“/one/! s/RE1/RE2/”。
这时只需要把中间的空格去掉就行了。

速度优化：
当由于某种原因（比如输入文件较大、处理器或硬盘较慢等）需要提高命令执行速度时，可以考虑在替换命令（“s/.../.../”）前面加上地址表达式来提高速度。
举例来说：
   sed 's/foo/bar/g' filename         # 标准替换命令
   sed '/foo/ s/foo/bar/g' filename   # 速度更快
   sed '/foo/ s//bar/g' filename      # 简写形式

当只需要显示文件的前面的部分或需要删除后面的内容时，可以在脚本中使用“q”命令（退出命令）。在处理大的文件时，这会节省大量时间。因此：
   sed -n '45,50p' filename           # 显示第45到50行
   sed -n '51q;45,50p' filename       # 一样，但快得多

如果你有其他的单行脚本想与大家分享或者你发现了本文档中错误的地方，请发电子邮件给本文档的作者（Eric Pement）。
邮件中请记得提供你所使用的sed版本、 该sed所运行的操作系统及对问题的适当描述。
本文所指的单行脚本指命令行的长度在65个字符或65个以下的sed脚本〔译注1〕。本文档的各种脚本是由以下所列作者所写或提供：
Al Aab                               # 建立了“seders”邮件列表
Edgar Allen                          # 许多方面
Yiorgos Adamopoulos                  # 许多方面
Dale Dougherty                       # 《sed & awk》作者
Carlos Duarte                        # 《do it with sed》作者
Eric Pement                          # 本文档的作者
Ken Pizzini                          # GNU sed v3.02 的作者
S.G. Ravenhall                       # 去html标签脚本
Greg Ubben                           # 有诸多贡献并提供了许多帮助
-------------------------------------------------------------------------
译注1：大部分情况下，sed脚本无论多长都能写成单行的形式（通过`-e'选项和`;'号）——只要命令解释器支持。
所以这里说的单行脚本除了能写成一行还对长度有所限制。因为这些单行脚本的意义不在于它们是以单行的形式出现。
而是让用户能方便地在命令行中使用这些紧凑的脚本才是其意义所在。

