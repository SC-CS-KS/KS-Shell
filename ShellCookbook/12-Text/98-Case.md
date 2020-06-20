# 98.文本处理 Shell 实例 

将输入信息转换为大写字符后再进行条件判断：
/> cat > test1.sh
#!/bin/sh
echo -n "Please let me know your name. "
read name
#将变量 name 的值通过管道输出到 tr 命令，再由 tr 命令进行大小写转换后重新赋值给 name 变量。
name=`echo $name | tr [a-z] [A-Z]`
if [[ $name == "STEPHEN" ]]; then
echo "Hello, Stephen."
else
echo "You are not Stephen."
fi
CTRL+D
/> ./test1.sh
Please let me know your name. stephen
Hello, Stephen.
