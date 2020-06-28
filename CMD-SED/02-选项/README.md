# 02.SED 选项

* -e script     
执行指定脚本，允许对同一行进行多次编辑，每一次编辑用到的命令都必须跟在一个-e后面。
比如下面一个命令：
sed –e ‘1,3d’ –e ‘s/piano/violin/’ datafile
这个命令对文件datafile中的每一行进行了两次编辑：第一次编辑将删除1到3行，第二次编辑将piano替换为violin。  
sed将对当前行按顺序执行两次编辑，然后再读入下一行，对下一行进行编辑，因此两次编辑命令的顺序不同会有不同的结果。  

* -i [扩展名]  
直接编辑文件，如果指定了扩展名，则先用它创建备份文件。  

* -f 脚本文件   
从文件中读取sed脚本执行  

* -n            
sed不写编辑行到标准输出，缺省为打印所有行(编辑和未编辑)，p命令可以用来打印编辑行。  

* -r           
sed默认支持basic RE，加-r选项则使用Extended规范。  

## 多点编辑：-e选项 

多点编辑：-e选项  
```bash
$ sed -e '1,5d' -e 's/test/check/' example  

(-e)选项允许在同一行里执行多条命令。如例子所示，第一条命令删除1至5行，第二条命令用check替换test。命令的执 行顺序对结果有影响。
如果两个命令都是替换命令，那么第一个替换命令将影响第二个替换命令的结果。  

#sed -e '1d' -e'2d' chan.t 
chenguang chan chen

$ sed --expression='s/test/check/' --expression='/love/d' example
一个比-e更好的命令是--expression。它能给sed表达式赋值。 

```
