# here 文档操作符

在shell脚本程序中，向一条命令传递输入的一种特殊方法是使用here文档。一个here document就是一段带有特殊目的的代码段。
它使用I/O重定向的形式将一个命令序列传递到一个交互程序或者命令中。
它允许一条命令在获得输入数据时就好像是在读取一个文件或键盘一样，而实际是从脚本程序中得到输入数据。

格式：
COMMAND <<SpecialString
...
SpecialString
SpecialString用来界定命令序列的范围，是一个特殊的字符序列，用来界定命令序列的范围，可自定义，但不能出现在传递命令的文档内容中。

使用举例：
1.使用cat打印多行消息，也可重定向哦（echo 有点麻烦了），同样支持参数替换哦
cat <<EOF > /tmp/test
this is here doc!
date
$HOME
EOF
this is here doc!
date
/home/xiangzi888
复制代码

2.设置变量
var=$(cat <<EOF
content
EOF
)

3. 广播: 将消息发送给每个登陆的用户
wall <<HALT
E-mail your noontime orders for pizza to the system administrator.
# more messages
# 注意: 'wall'命令会把注释行也打印出来. 
HALT

4.带有抑制tab功能的多行消息（去掉每行前面的TAB字符）
cat <<-EOF
this is here doc!
date doesn't work
EOF

5.关闭变量替换的功能  
cat <<'EOF'
$HOME
doesn't work here！
EOF

6.生成另外一个脚本（比较诡异！）
(cat <<'EOF'
#!/bin/bash
#  Note that since we are inside a subshell,
#+ we can't access variables in the "outside" script.
echo "Generated file will be named:  /tmp/script.sh"
#  Instead, the result is literal output.
a=7
b=3
c=$(($a * $b))
echo "c = $c"
exit 0
EOF
) > /tmp/script.sh

7.here doc与函数
#!/bin/bash
#这个函数看起来就是一个交互函数, 但是... 
GetPersonalData ()
{
    read firstname
    read lastname
    read address
}

# 给上边的函数提供输入.
GetPersonalData <<DATA
Robert
Bozeman
Hust
DATA

8.‘匿名’here文档，不显示！
（利用 : 可以注释掉一段代码块，或者写一个自文档化(self-documenting)的脚本。
详见：http://www.tsnc.edu.cn/default/tsnc_wgrj/doc/abs-3.9.1_cn/html/here-docs.html）
#!/bin/bash
# 如果其中某个变量没被设置, 那么就打印错误信息.  
: <<TESTVARIABLES
${HOSTNAME?}${USER?}${MAIL?}
TESTVARIABLES

exit 0

9.一些注意事项
a.某些工具是不能放入here document中运行的。
b.结尾的limit string, 就是here document最后一行的limit string, 必须从第一个字符开始，它的前面不能够有任何前置的空白。
而在这个limit string后边的空白也会引起异常，空白将会阻止limit string的识别。
c.对于那些使用"here document"，并且非常复杂的任务, 最好考虑使用expect脚本语言，这种语言就是为了达到向交互程序添加输入的目的而量身定做的。
