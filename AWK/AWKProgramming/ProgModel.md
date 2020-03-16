
# awk 流程控制结构 

流程控制结构 (基本上是使用C语言的语法) 
其中condition一般为布尔表达式，body和else-body是awk语句块。 
① if (condition) 
   {then-body} 
   [else {else-body}] 

② while (condition) 
   {body} 

③ do 
   {body} 
   while (condition)

④ for (initialization; condition; increment)  与C语言的for结构的语法相同。 
       {body}       
                               
⑤ break : 
   跳出包含它的for、while、do-while 循环。

⑥ continue : 
   跳过for、while、do-while循环的body的剩余部分，而立刻进行下一次循环的执行。 

# awk 结构与运行

## awk_script的组成:
① awk_script 可以由一条或多条awk_cmd组成，两条awk_cmd之间一般以NEWLINE（\n）分隔。
② awk_cmd   由两部分组成: 
   awk_pattern { actions } 
③ awk_script  可以被分成多行书写，必须确保整个awk_script被单引号括起来。

## awk命令的一般形式: 
awk ' BEGIN { actions } awk_pattern1 { actions } ............ awk_patternN { actions } END { actions } ' inputfile 
其中 BEGIN { actions } 和 END { actions } 是可选的。 

## awk的运行过程: 
① 如果BEGIN 区块存在，awk执行它指定的actions。 
② awk从输入文件中读取一行，称为一条输入记录。(如果输入文件省略，将从标准输入读取)。
③ awk将读入的记录分割成字段，将第1个字段放入变量$1中，第2个字段放入$2，以此类推。$0表示整条记录。
   字段分隔符使用shell环境变量IFS或由参数指定。 
④ 把当前输入记录依次与每一个awk_cmd中awk_pattern比较，看是否匹配，如果相匹配，就执行对应的actions。
   如果不匹配，就跳过对应的actions，直到比较完所有的awk_cmd。 
⑤ 当一条输入记录比较了所有的awk_cmd后，awk读取输入的下一行，继续重复步骤③和④，这个过程一直持续，直到awk读取到文件尾。 
⑥ 当awk读完所有的输入行后，如果存在END，就执行相应的actions。 

## awk 执行注意点

* 04.iput_file可以是多于一个文件的文件列表，awk将按顺序处理列表中的每个文件。 

* 05.
一条awk_cmd 的awk_pattern可以省略，省略时不对输入记录进行匹配比较就执行相应的actions。
一条awk_cmd 的actions也可以省略，省略时默认的动作为打印当前输入记录(print $0) 。
一条awk_cmd 中的awk_pattern和actions不能同时省略。 

* 06
BEGIN区块和END区块别位于awk_script的开头和结尾。
awk_script中只有END区块或者只有BEGIN区块是被允许的。
  
如果awk_script中只有BEGIN { actions } ，awk不会读取input_file。 
注意：
利用awk的上述这一特性，可以利用awk一些非文件处理的事情，
例如，通过match函数来判断一个字符串是否是数字，要判断的字符串可以通过awk的参数列表来传入。用ARGV数据访问。

* awk把输入文件的数据读入内存，然后操作内存中的输入数据副本，awk不会修改输入文件的内容。 

* awk的总是输出到标准输出，如果想让awk输出到文件，可以使用重定向。 

# awk pattern 

awk_pattern模式部分决定actions动作部分何时触发。

# awk actions 

actions就是对awk读取的记录数据进行的操作，actions由一条或多条语句或者命令组成，语句、命令之间用分号(;)分隔。
actions中还可以使用流程控制结构的语句。 



