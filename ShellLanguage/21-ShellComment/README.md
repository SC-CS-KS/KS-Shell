# 21.Shell 注释

## Shell 多行注释

把shell多行注释掉，有如下方法：
第一种：基于Here Documents和:实现
如果被注释的内容中有反引号会报错
:<<BLOCK
....被注释的多行内容
BLOCK

解决注释中有反引号的问题
:<< 'BLOCK
....被注释的多行内容
BLOCK'
或者干脆只留单引号
:<< '
....被注释的多行内容
'

第二种：当注释内容中有括号时报语法错误错，但里面有反引号,引号时没有问题
:||{
....被注释的多行内容
}

第三种：会对注释内容中的括号引号等语法错误报错
if false ; then
....被注释的多行内容
fi
---------------------------------- 华丽的分割线 -------------------------------------
here 文档与重定向输入
here 文档 为需要输入数据的程序(如 mail sor 或cat) 接收内置文本，
直至用户自定义的休止符。

$ cat << FINISH
> Hello there $LOGNAME
> The time is `data`
> If you want to know who is god, type "echo \$LOGNAME" 
> FINISH

here 文档常被shell脚本用来生成 菜单 或 被用来 多行注释
用 here 文档 和 case 命令生成菜单
# file ./profile

echo "select a terminal type: "
cat << ENTER
1) vt 120
2) wyse50
3) sun
ENTER

read choice

case "$choice" in
1) TERM=vt120
export TERM
;;
2) TERM=wyse50
export TERM
;;
3) TERM=sun
export TERM
;;
esac

echo "TERM is $TERM"
