# 06.Shell Process Substitution

Bash Process Substitution
In addition to the fairly common forms of input/output redirection the shell recognizes something called process substitution. Although not documented as a form of input/output redirection, its syntax and its effects are similar.
The syntax for process substitution is:
<(list)
or
>(list)
where each list is a command or a pipeline of commands. The effect of process substitution is to make each list act like a file. This is done by giving the list a name in the file system and then substituting that name in the command line. The list is given a name either by connecting the list to named pipe or by using a file in /dev/fd (if supported by the O/S). By doing this, the command simply sees a file name and is unaware that its reading from or writing to a command pipeline.
To substitute a command pipeline for an input file the syntax is:
command ... <(list) ...
To substitute a command pipeline for an output file the syntax is:  command ... >(list) ...
At first process substitution may seem rather pointless, for example you might imagine something simple like:
uniq <(sort a)
to sort a file and then find the unique lines in it, but this is more commonly (and more conveniently) written as:  sort a | uniq
The power of process substitution comes when you have multiple command pipelines that you want to connect to a single command.
For example, given the two files:
# cat a
e
d
c
b
a
# cat b
g
f
e
d
c
b
To view the lines unique to each of these two unsorted files you might do something like this:  # sort a | uniq >tmp1
# sort b | uniq >tmp2
# comm -3 tmp1 tmp2
a
f
g
# rm tmp1 tmp2
With process substitution we can do all this with one line:  # comm -3 <(sort a | uniq) <(sort b | uniq)
a
f
g
Depending on your shell settings you may get an error message similar to:
syntax error near unexpected token `('
when you try to use process substitution, particularly if you try to use it within a shell script. Process substitution is not a POSIX compliant feature and so it may have to be enabled via:  set +o posix
Be careful not to try something like:  if [[ $use_process_substitution -eq 1 ]]; then
set +o posix
comm -3 <(sort a | uniq) <(sort b | uniq)
fi
The command set +o posix enables not only the execution of process substitution but the recognition of the syntax. So, in the example above the shell tries to parse the process substitution syntax before the "set" command is executed and therefore still sees the process substitution syntax as illegal.
Of course, note that all shells may not support process substitution, these examples will work with bash.
__________________________ 
Mitch Frazier is the System Administrator at Linux Journal.
---------------------------------------------------------------------------------------------------------------------------
在支持命名管道(FIFOs)或者'/dev/fd'的系统上可以实现Process Substitution,基本格式如下
    command <(list)
    or  
    command >(list)
开始list的运行的时候会把list的输入输出连接到一个FIFO或者某个`/dev/fd'下的文件。
而整个扩展的结果就是把这个文件的文件名作为参数传递给命令command。

如果使用>(list)的话，command写入这个文件的内容，就会作为list的输入。
而如果使用<(list)的话， 那么list的输出就可以使command从这个文件中读到。
注意<，>与左括号之间是没有空格的，否则的话会被解释成为重定向操作。
在允许的情况下 process substitution和parameter expansion， variable expasion, command expansion 和arithmatic expasion一起被执行.

好处，能解决一些重定向不能解决的问题，如让一个命令有多个输入的情况：
有一个例子，要求将2个文件，a和b的内容进行查找，找到唯一的行，然后输出。
下面是采用redirection的方法：
# sort a | uniq >tmp1
# sort b | uniq >tmp2
# comm -3 tmp1 tmp2
a
f
g
# rm tmp1 tmp2
如果使用process substitution，可以这样做：
# comm -3 <(sort a | uniq) <(sort b | uniq)
a
f
g
 这样明显效率提高了。
---------------------------------------------------------------------------------------------------------------------------
cmd|→tee--→stdout→|cmd2
            ↓            ↑
             →cmd1→
             
echo 123 | tee >(awk '{ print "Length of " $0 " is " length($0) }') | tr 3 c
12c
Length of 12c is c

echo 123 | tee >(awk '{ print "Length of " $0 " is " length($0) }' >tmpfile) | tr 3 c ;cat tmpfile
12c
Length of 123 is 3

echo "Hello from: "|tee > >(sed 's/$/sed 1/') >(sed 's/$/sed 2/') >(awk '{printf $0"awk\n"}')
---------------------------------------------------------------------------------------------------------------------------
Q：
现在有一个进程替换的问题向大家请教：
当直接在bash的交互式命令中输入：
while read name; do echo $name; done< <(echo "
dfasdf
dsfa
afda"|grep -Ev "(dsfa)|(^ *$)")
这样执行的话是没有问题的，但当把它放入到一个脚本文件中进行执行的时候：
#!/bin/bash
while read name; do echo $name; done< <(echo "
dfasdf
dsfa
afda"|grep -Ev "(dsfa)|(^ *$)") 

却出现了如下的错误：
test.sh: line 3: syntax error near unexpected token `<'
test.sh: line 3: `while read name; do echo $name; done< <(echo "'

A：
在一般的linux系统当中，使用sh调用执行脚本相当于打开了bash的POSIX标准模式也就是说 /bin/sh 相当于 /bin/bash --posix
所以，sh跟bash的区别，实际上就是bash有没有开启posix模式的区别,而进程替换非POSIX兼容的，所以会出现错误。
因为main (int argc, char ** argv ) , argv[0] 可以取得对应可执行文件的名字, 在sh->bash情况下, argv[0] == 'sh'，所以就可以区别对待了!

而如果直接使用./test.sh 执行，脚本使用第一行指定的解释器来解释执行。
---------------------------------------------------------------------------------------------------------------------------
