
# 01.AWK Variable
在awk_script中的表达式中要经常使用变量，不要给变量加双引号，那样做，awk将视之为字符串。

## 01.AWK 内部变量

awk的内部变量用于存储awk运行时的各种参数，这些内部变量又可以分为: 

### 自动内部变量
这些变量的值会随着awk程序的运行而动态的变化，在awk_script中改变这些变量的值是没有意义的(即不应该被赋值)。
常见的有:
NF
当前输入行的字段数
NR
awk当前已处理的行数，多个文件时会累加
FNR
awk处理的当前文件，已被处理的行数。
当输入文件只有一个时，FNR和NR是一致的。
当多个文件时，没处理完一个文件，在处理下一个文件时FNR清零。
FILENAME
当前输入文件的文件名。
ARGC
命令行参数个数。(不包括选项和awk_script，实际就是输入文件的数目加1)
ARGIND
当前被处理的文件在数组ARGV内的索引
( 实际上ARGV[1]就是第一个输入文件，第一个文件的索引就是1，ARGIND主要用在判断当前处理文件是哪一个) 
举例: 
awk '{print NR,NF,$0} END {print FILENAME}' input_file 

### 字段变量($0 $1 $2 $3 ...)
   当awk把当前输入记录分段时，会对这些字段变量赋值。
   和内部变量类似，在awk运行过程中字段变量的值是动态变化的。不同的是，修改这些字段变量的值是有意义的，被修改的字段值可以反映到awk的输出中。 

   可以创建新的输出字段。
   比如，当前输入记录被分割为8个字段，这时可以通过对变量 $9 (或$9之后的字段变量)赋值而增加输出字段，NR的值也将随之变化。
   eg：
   awk -F : '$8=$1 {print $8}' passwd.t  

   字段变量支持变量名替换。 
   举例: pwd |awk -F/ '{print $NF}'             // print $NF 打印输入记录的最后一个字段 
         awk '{x=2;print $x}' input_file        // 打印输入记录的第2个字段 

### 其它内部变量: 
   可以修改这些变量。
   常见的有: 
   FS :     输入记录的字段分隔符(默认是空格和制表符) 
   OFS :    输出记录的字段分隔符(默认是空格) 
   OFMT :   数字的输出格式(默认是 %.6g) 
   RS :     输入记录间的分隔符(默认是NEWLINE) 
   ORS :    输出记录间的分隔符(默认是NEWLINE) 
   ARGV :   命令行参数数组 
   ENVIRON :  存储系统当前环境变量值的数组，它的每个成员的索引就是一个环境变量名，而对应的值就是相应环境变量的值。
              可以通过给ENVIRON数组的成员赋值而改变环境变量的值，但是新值只在awk_script内有效。
              eg: ENVIRON["HISTSIZE"]=500 
                  cat /etc/passwd | awk 'BEGIN { FS=":" } {print "User name: "$1,"UID: "$4}' 

ARGV是一个数组，用于记录命令行上参数，其个数由ARGC指定，数组下标从0开始。
  eg：#awk 'BEGIN{for (i in ARGV) print ARGV[i]}' whoawk
      awk
      whoawk

或者写成 #awk  'BEGIN {i=0; for(;i<ARGC;i++){ print ARGV[i]}}' whoawk

//要写在BEGIN,否则以上输出会重复三遍，因为whoawk中有三行内容。

## 02.AWK 自定义变量

01. 定义变量: 
   varname=value 
   (自定义变量不需先声明后使用，赋值语句同时完成变量定义和初始化) 

02. 在表达式中出现不带双引号的字符串都被视为变量，如果之前未被赋值，默认值为0或空字符串。

03. 向命令行awk程序传递变量的值: 
① Usage: 
   awk 'awk_script' awkvar1=value1 awkvar2=value2 .... input_file 
   eg: awk '{if ($5 < ARG) print $0 }' ARG=100 input_file 

② awkvar可以是awk内置变量或自定义变量。 

③ awkvar的值将在awk开始对输入文件的第一条记录应用awk_script前传入。
   如果在awk_script中已经对某个变量赋值，那么在命令行上传到该变量的值就会无效
   (实际上是awk_script中的赋值语句覆盖了从命令行上传入的值)。 

④ 在awk脚本程序中不能直接使用shell的变量。
    通过使用下面的语法可达到这样的效果。 
    awk 'awk_script' awkvar1=shellvar1 awkvar2=shellvar2 .... input_file 

    eg: 
    awk '{if (v1 == "root") {print "User name is root!"}}' v1=$USER input_file
 
⑤ 可以向awk脚本传递变量的值，与上面的类似。 
   awk_script_file awkvar1=value1 awkvar2=value2 ... input_file 

## 10.AWK 变量作用域

本文从两个错误的例程开始，提出 Awk 中全局变量污染的现象，并分析其发生的原因。
接下来，针对 Awk 变量作用域的特点，提出两种避免全局变量污染的常用方法，引出 Awk 中定义局部变量的方法，并提出修改过后的代码。
然后，通过 Awk 的变量调试功能，提出修改过后代码存在的不足，并引出编写通用函数应注意的地方。
最后，通过简单说明 Awk 中包含头文件的方法，倡导大家更科学，更有效的使用 Awk 这一文本处理利器。
文章的结尾提供了一些常用的 Awk 参考文档，供大家学习参考。

在 C、PHP 等大多数程序语言中，函数内声明的变量都会自动成为局部变量，变量的生命周期只在函数执行期，函数返回时变量自动销毁。
在 Awk 使用的大多数情况下，我们用到的都是全局变量，不曾关心过变量作用域问题。
在几十上百行的小型脚本中，一线到底的脚本的确无可厚非，没有因函数调用引起的变量交叉引用，固然也不会发生全局变量污染。
随着脚本规模的扩大，必将以结构化和模块化的方式来编写 Awk 脚本，自定义函数也就成了家常便饭。
也许某一天增加一个小功能后发现结果不如所意，而这段代码改得如此的清晰以至于没有人会怀疑它的正确性。
判断得出问题一定是历史性的，但是调试这种问题显然是既费时又费力的。
笔者曾经在这个问题上花费过将近半天的时间，饱受调试痛苦之后，才注意到 Awk 中变量作用域的问题。
在此把这些经验教训总结成文，以供大家参考。

变量污染发生的情况
让我们通过两个例程来看看变量污染发生的情况：
清单 1. fac1.awk 打印 1 到 10 的阶乘
      # fac1.awk
      # original version of fac1.awk
      #
     
      function factorial(n)
      {
          s=1;
      
          for (i=1; i<=n; i++)
          {
             s *= i;
          }
     
          return s;
      }
     
      {
          for (i=1; i<=10; i++)
          {
              value = factorial(i);
              printf("fac(%d) = %d/n", i, value);
          }
      }
     
运行并查看结果：
```bash
# echo "" | awk -f fac1.awk
fac(2) = 1
fac(4) = 6
fac(6) = 120
fac(8) = 5040
fac(10) = 362880
```
打印只显示出 2, 4, 6, 8, 10 的阶乘，而且结果还不对。很明显程序执行流程出了问题。原因在哪里呢？
由于这个程序比较简单，经过简单分析可以得到全局变量 i 在自定义函数 factorial() 中被覆盖，影响了程序的工作流程，导致结果异常。

上例中 i 这样的循环变量受影响，是最典型的全局变量受污染的例子。
另外，在一些递归形式的函数实现中，也很有可能出现变量污染的情况。
让我们来看一看下面的例程：
清单 2. fac2.awk 打印 1 到 10 的阶乘（函数以递归方式实现）
```awk
       # fac2.awk
       # original version of fac2.awk
       #
      
      function factorial(n)
      {
          if (n == 1)
          {
             i = 1;
             return i;
          }
         else
         {
             i = factorial(n-1) * n;
             return i;
         }
      }
     
      {
          for (i=1; i<=10; i++)
          {
              value = factorial(i);
              printf("fac(%d) = %d/n", i, value);
          }
      }
```
运行并查看结果：
```bash
#echo "" | awk -f fac2.awk
fac(1) = 1
fac(2) = 2
fac(6) = 6
fac(5040) = 5040
```

结果也很奇怪。究其原因，还是全局变量 i 受污染所致。
虽然上面这一段程序造得有点生硬，一般情况下不会在函数中使用 i 来处理函数的返回值，不过暴露出来的问题正在 Awk 中全局变量受污染的问题。
以上两个例程出现问题的原因是全局变量污染，但是发生全局变量污染的原因又是什么呢？ gawk 用户手册中有提到，在传统的 Awk 中，是不支持函数的，程序顺序解释并执行，第一次出现的变量随即完成初始化，在以后的代码中得以引用。我们目前在 Linux 下使用的大多数是 gawk ，它是 Awk 的一个扩展实现。在原来 Awk 处理变量的原则下，gawk 引用了自定义函数，改变了原有顺序执行流程，即代码可能跳转。在没有局部变量实现的 Awk 中，变量污染就发生了。接下来我们将分析如何避免因为历史原因造成的全局变量污染问题。

避免变量污染的方法
以上两个错误的例程简单说明了在 Awk 中易导致变量污染的常见情况。
那么防止这种情况的方法有哪些呢？最笨的办法，就是使函数内使用的变量与全局变量不重名。
笔者很久以前使用的就是这个笨方法，对于每个函数内的“局部变量”，都以自定义的函数名称缩写作为开头，以防止在全局范围内的变量名冲突。、在一定程度上，这的确是一种有效的方法。但是从逻辑上来讲，这种方法决不是万全之计。
理想的解决方案当然是在函数内定义局部变量，其实也是最容易想到的方法。
在 C 语言中，函数内定义的变量自动成为局部变量，在函数结束时自动销毁。
在 bash 中，虽然也有跟 Awk 类似的全局变量污染的情况，但是可以通过 local 关键字完成函数内局部变量的声明，来避免全局变量污染问题。
在 Awk 中如何定义局部变量这个问题好像不是那么明显，至少在 gawk 手册的 “VARIABLES” 一节中没有关于局部变量的任何线索。
非常有趣的是，虽然大部分人第一个想到是这个方案，但最终都走回了第一个方案。
笔者也是在一次偶然的闲暇时光重读 sed & awk 一书时，发现有这样一段说明：
“Awk 提供了一种蹩脚的方式来定义局部变量，那就是通过函数的参数列表。”
不久之后，笔者又在 gawk 手册中 “USER-DEFINED FUNCTIONS” 一节中找到了相似的一段话：
“由于原来的 Awk 不支持函数，局部变量在 Awk 中的实现相当笨拙，通过给函数定义额外的参数来实现。
按照惯例，在真实参数后面多加几个空格，以分隔真实参数与局部变量声明。”
局部变量定义的问题解决之后，让我们回到刚才有问题的程序，以 fac1.awk 为例，我们来看修改过后的代码：

清单 3. fac1-2.awk 传统的局部变量定义
```awk               
      1 #
      2 # fac1-2.awk
      3 # version 0.2 of fac1.awk
      4 #
      5
      6 function factorial(n,    i)
      7 {
      8     s=1;
      9
     10     for (i=1; i<=n; i++)
     11     {
     12         s *= i;
     13     }
     14
     15     return s;
     16 }
     17
     18 {
     19     for (i=1; i<=10; i++)
     20     {
     21         value = factorial(i);
     22         printf("fac(%d) = %d/n", i, value);
     23     }
     24 }
     25
``` 

运行并查看结果：
```bash
[robert@saphires awk_var]$ echo "" | awk -f fac1-2.awk
fac(1) = 1
fac(2) = 2
fac(3) = 6
fac(4) = 24
fac(5) = 120
fac(6) = 720
fac(7) = 5040
fac(8) = 40320
fac(9) = 362880
fac(10) = 3628800
```
是的，就这么简单，问题解决了！正如手册中所说，这是一个历史问题，通过形参来定义函数内局部变量只是一个折衷解决方案。对于不知道其中奥妙的人来讲，原来的函数就成了一个可变参数的函数。对于上面的例子，既可以通过 factorial(n) 调用，也可以通过 factorial(n, i) 调用，对于不明白的代码阅读者或维护者很可能就此陷入困境。因为在函数的定义中虽然有两个形参，实际上第二个是我们不希望在函数调用时引用的。
比起书上写的加空格的方法，笔者更倾向于在正常的形参后面加一个名为 _ARGVEND_ 的参数，表示正常调用所需的形参到此结束，在此标识以后的形参都是“假形参”，实际上只是局部变量的定义。天知道哪天谁看到这样的多个空格分隔参数的函数会抱怨说“这是哪个蹩脚程序员写的函数声明”然后把这几个空格去掉甚至在函数调用时引用呢。有了这样的标识，那个真正的“蹩脚程序员”至少会想一下为什么这里会有个 _ARGVEND_ 呢？当然这个标识你可以使用任何你喜欢的，但是有一点你必须注意，传统毕竟是传统，在笔者提出的方式成为传统之前，你必须知道多个空格之间的参数是怎么回事，以免让你自己成为那个真正的“蹩脚程序员”。下面是以笔者的方式修改过后的代码：

清单 4. fac1-3.awk 笔者的局部变量定义  
```awk          
      1 #
      2 # fac1-3.awk
      3 # version 0.3 of fac1.awk
      4 #
      5
      6 function factorial(n, _ARGVEND_, i)
      7 {
      8     s=1;
      9
     10     for (i=1; i<=n; i++)
     11     {
     12         s *= i;
     13     }
     14
     15     return s;
     16 }
     17
     18 {
     19     for (i=1; i<=10; i++)
     20     {
     21         value = factorial(i);
     22         printf("fac(%d) = %d/n", i, value);
     23     }
     24 }
```
运行并查看结果：
```bash
[robert@saphires awk_var]$ echo "" | awk -f fac1-3.awk
fac(1) = 1
fac(2) = 2
fac(3) = 6
fac(4) = 24
fac(5) = 120
fac(6) = 720
fac(7) = 5040
fac(8) = 40320
fac(9) = 362880
fac(10) = 3628800
```
调试全局变量
上面谈到了变量污染发生的条件以及解决方案。这里再补充一下出现相关问题时的一个简单调试方法。
那就是 awk 的 -dump-variables 参数，它可以把程序运行结束过后的所有全局变量打印到一个文本文件提供调试。
我们来看下面的例子，对 fac1-3.awk 执行的输出结果：
```bash
ass=code-outline>
 [robert@saphires awk_var]$ echo "" | awk -f fac1-3.awk --dump-variables=/tmp/var.dump
fac(1) = 1
fac(2) = 2
fac(3) = 6
fac(4) = 24
fac(5) = 120
fac(6) = 720
fac(7) = 5040
fac(8) = 40320
fac(9) = 362880
fac(10) = 3628800

[robert@saphires awk_var]$ cat /tmp/var.dump
ARGC: number (1)
ARGIND: number (0)
ARGV: array, 1 elements
BINMODE: number (0)
CONVFMT: string ("%.6g")
ERRNO: number (0)
FIELDWIDTHS: string ("")
FILENAME: string ("-")
FNR: number (1)
FS: string (" ")
IGNORECASE: number (0)
LINT: number (0)
NF: number (0)
NR: number (1)
OFMT: string ("%.6g")
OFS: string (" ")
ORS: string ("/n")
RLENGTH: number (0)
RS: string ("/n")
RSTART: number (0)
RT: string ("")
SUBSEP: string ("/034")
TEXTDOMAIN: string ("messages")
i: number (11)
s: number (3628800)
value: number (3628800)
```
我们可以看到，s 这个在 factorial() 函数中变量其实是被当成了一个全局变量，虽然 fac1-3.awk 的运行结果已经完全符合我们的功能需要，但是如果把这个叫 factorial() 的函数移植到另外一个不是我们写的代码里面去，天知道会不会与其它一个叫 s 的全局变量混用引起变量污染呢？如果那一天真的到来的话，也许我们要花上好几个小时才能找出这样的污染根源。如何在第一时间避免这样的灾难发生呢？当然是把 s 也定义成局部变量，示例如下：
清单 5. fac1-4.awk 最终版本
```awk               
      1 #
      2 # fac1-4.awk
      3 # version 0.4 of fac1.awk
      4 #
      5
      6 function factorial(n, _ARGVEND_, i, s)
      7 {
      8     s=1;
      9
     10     for (i=1; i<=n; i++)
     11     {
     12         s *= i;
     13     }
     14
     15     return s;
     16 }
     17
     18 {
     19     for (i=1; i<=10; i++)
     20     {
     21         value = factorial(i);
     22         printf("fac(%d) = %d/n", i, value);
     23     }
     24 }
     25
```
然后我们再看一下 fac1-4.awk 的 vardump ：
```bash
[robert@saphires awk_var]$ echo "" | awk -f fac1-4.awk --dump-variables=/tmp/var.dump
fac(1) = 1
fac(2) = 2
fac(3) = 6
fac(4) = 24
fac(5) = 120
fac(6) = 720
fac(7) = 5040
fac(8) = 40320
fac(9) = 362880
fac(10) = 3628800
[robert@saphires awk_var]$ cat /tmp/var.dump
......(omitted)
i: number (11)
value: number (3628800)
```
从上面的结果可以看到，在程序运行结束过后，只有两个我们觉得应该有的全局变量 i 和 value 存在。这样， factorial() 函数基本上可以作为一个通用函数被移植到其它的 Awk 脚本中去了。通过对 Awk 全局变量的调试输出，我们也得出一个书写 Awk 函数需要注意的原则：那就是只要是局部变量，都应该在参数列表中进行定义，只有这样才能完全避免全局变量污染发生。
 
使用包含文件
上面的 fac1-4.awk 中的 factorial() 函数已经可以视为安全的 Awk 函数了。
在 Awk 的应用中，是否可以象C语言的#include或bash中的source一样包含其它源文件呢？
答案是可以的。让我们先把 factorial() 函数单独存到 fac-lib.awk 中：
```bash
清单 6. fac-lib.awk Awk 函数库            
      1 #
      2 # library for awk
      3 #
      4
      5 function factorial(n, _ARGVEND_, i, s)
      6 {
      7     s=1;
      8
      9     for (i=1; i<=n; i++)
     10     {
     11         s *= i;
     12     }
     13
     14     return s;
     15 }
     16
```
一种方法是通过引用多个 awk 脚本来实现，这种方式不需要有任何包含源文件相关的标志： 
清单 7. fac3.awk 不包含 Awk 函数库的主程序
```bash
      1 #
      2 # fac3.awk
      3 # original version of fac3.awk
      4 #
      5
      6 {
      7     for (i=1; i<=10; i++)
      8     {
      9         value = factorial(i);
     10         printf("fac(%d) = %d/n", i, value);
     11     }
     12 }
     13
```

运行并查看结果：
```bash
#echo "" | awk -f fac-lib.awk -f fac3.awk
fac(1) = 1
fac(2) = 2
fac(3) = 6
fac(4) = 24
fac(5) = 120
fac(6) = 720
fac(7) = 5040
fac(8) = 40320
fac(9) = 362880
fac(10) = 3628800
```
下面这种方式则更象其它语言中的源代码包含，但是对于有函数包含的 Awk 脚本，我们需要用 igawk 来执行。
igawk 实际上也只是一个脚本，它在运行时分析 Awk 脚本中的 @include 标志，并把 @include 包含的文件合并到当前脚本的 @include 行，然后进行解释执行。
一方面，Awk 所谓的包含功能也这么的不地道，另一方面，这个包含功能的扩展本身也是由 Awk 完成的，非常有趣。

下面来看一下利用 igawk 运行包含函数库的 Awk 脚本。
清单 8. fac3-2.awk 包含 Awk 函数库的主程序
```bash
      1 #
      2 # fac3-2.awk
      3 # original version of fac3-2.awk
      4 #
      5
      6 @include fac-lib.awk
      7
      8 {
      9     for (i=1; i<=10; i++)
     10     {
     11         value = factorial(i);
     12         printf("fac(%d) = %d/n", i, value);
     13     }
     14 }
     15
```
运行并查看结果：
```bash
 [robert@saphires awk_var]$ echo "" | igawk -f fac3-2.awk
fac(1) = 1
fac(2) = 2
fac(3) = 6
fac(4) = 24
fac(5) = 120
fac(6) = 720
fac(7) = 5040
fac(8) = 40320
fac(9) = 362880
fac(10) = 3628800
```
## 20.AWK 获取外部变量

01.获得普通外部变量
$ test='awk code'                            
$ echo | awk  '{print test}' test="$test"
awk code
$ echo | awk  test="$test" '{print test}' 
awk: cmd. line:1: fatal: cannot open file `{print test}' for reading (No such file or directory)
 
格式如：
awk '{action}'  变量名=变量值   ，这样传入变量，可以在action中获得值。 注意：变量名与值放到’{action}’后面。

$ echo | awk  'BEGIN{print test}' test="$test"         
这种变量在：BEGIN的action不能获得。

02.BEGIN程序块中变量
$ test='awk code'                                 
$ echo | awk -v test="$test" 'BEGIN{print test}'
awk code
$ echo | awk -v test="$test" '{print test}'     
awk code
格式如：
awk –v 变量名=变量值 [–v 变量2=值2 …] 'BEGIN{action}'  
注意：
用-v 传入变量可以在三种类型的action 中都可以获得到，但顺序在action前面。

03.获得环境变量
$ awk  'BEGIN{for (i in ENVIRON) {print i"="ENVIRON[i];}}'
AWKPATH=.:/usr/share/awk
SSH_ASKPASS=/usr/libexec/openssh/gnome-ssh-askpass
SELINUX_LEVEL_REQUESTED=
SELINUX_ROLE_REQUESTED=
LANG=en_US.UTF-8
.......

awk内置变量 ENVIRON 就可以直接获得环境变量，它是一个字典数组，环境变量名就是它的键值。
