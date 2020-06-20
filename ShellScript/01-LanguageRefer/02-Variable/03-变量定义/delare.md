# declare

## declare 使用

declare或typeset为内建命令(它们是完全相同的)，可以用来限定变量的属性，这是在某些编程语言中使用的定义类型不严格的方式。
命令declare是bash版本2之后才有的。命令typeset也可以在ksh脚本中运行。
---------------------------------------------------------------------------------------------------
declare/typeset 选项

-r 只读    
declare -r var1
(declare -r var1与readonly var1作用相同)
这大致和C的const限定词相同。一个试图改变只读变量值的操作将会引起错误信息而失败。
-------------------------------------------------------------------
-i 整数   
declare -i number
# 脚本余下的部分会把"number"当作整数看待.		
number=3
echo "Number = $number"     # Number = 3
   
number=three
echo "Number = $number"     # Number = 0

# 脚本尝试把字符串"three"作为整数来求值(译者注：当然会失败，所以出现值为0)。

某些算术计算允许在被声明为整数的变量中完成，而不需要特别使用expr或let来完成。
n=6/3
echo "n = $n"       # n = 6/3
    
declare -i n
n=6/3
echo "n = $n"       # n = 2
-------------------------------------------------------------------
-a 数组
declare -a indices
变量indices会被当作数组.
-------------------------------------------------------------------
-f 函数
declare -f
在脚本中没有带任何参数的declare -f 会列出所有在此脚本前面已定义的函数出来。

declare -f function_name
而declare -f function_name则只会列出指定的函数.
-------------------------------------------------------------------
-x export
declare -x var3
这样将声明一个变量作为脚本的环境变量而被导出。
-------------------------------------------------------------------
-x var=$value
declare -x var3=373
declare命令允许在声明变量类型的时候同时给变量赋值。
---------------------------------------------------------------------------------------------------
例子：
用declare来声明变量类型
   #!/bin/bash
   
   func1 ()
   {
       echo This is a function.
   }
   declare -f                    # 列出上面的函数.
   echo
   declare -i var1               # var1是一个整数.
   var1=2367
   echo "var1 declared as $var1"
   var1=var1+1                   # 整数声明后，不需要使用'let'.
   echo "var1 incremented by 1 is $var1."
                                 # 试图将已声明为整数的变量的值更改为浮点值.
   echo "Attempting to change var1 to floating point value, 2367.1."
   var1=2367.1                   # 引起一个错误信息，此变量的值保持原样.
   echo "var1 is still $var1"
   echo
   declare -r var2=13.36         # 'declare'允许设置变量的属性，同时也给变量赋值
   echo "var2 declared as $var2" # 试图更改只读变量的值
   var2=13.37                    # 引起错误，并且从脚本退出
   echo "var2 is still $var2"    # 这行不会被执行
   exit 0                        # 脚本不会从这儿退出
-------------------------------------------------------------------
使用内建的declare来限定变量的范围
    foo ()
    {
        FOO="bar"
    }
    
    bar ()
    {
        foo
        echo $FOO
    }
---------------------------------
    foo ()
    {
        declare FOO="bar"
    }
    
    bar ()
    {
       foo
       echo $FOO
    }
---------------------------------  
执行上面两个脚本，第一个打印"bar"，第二个什么也不打印。
---------------------------------------------------------------------------------------------------


declare: declare [-afFirtx] [-p] [name[=value] ...]
    Declare variables and/or give them attributes.  If no NAMEs are
    given, then display the values of variables instead.  The -p option
    will display the attributes and values of each NAME.
    
    The flags are:
      -a        to make NAMEs arrays (if supported)
      -f        to select from among function names only
      -F        to display function names (and line number and source file name if
        debugging) without definitions
      -i        to make NAMEs have the `integer' attribute
      -r        to make NAMEs readonly
      -t        to make NAMEs have the `trace' attribute
      -x        to make NAMEs export
    
    Variables with the integer attribute have arithmetic evaluation (see
    `let') done when the variable is assigned to.
    
    When displaying values of variables, -f displays a function's name
    and definition.  The -F option restricts the display to function
    name only.
    
    Using `+' instead of `-' turns off the given attribute instead.  When
    used in a function, makes NAMEs local, as with the `local' command.
---------------------------------------------------------------------------------------------------
