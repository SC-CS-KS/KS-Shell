# 06.变量查找

${!var@}
用于返回当前shell中，变量名以var开始的变量；这种格式跟第2种很相似，可是功能却完全不同。

var1=hello1
var2=hello2

echo ${!var@}
>>> var1 var2

var=hello

echo ${!var@}
>>> var var1 var2

变量的输出，不是以定义的先后为顺序的，这一点需要注意。


## ${!var}

${!var}
用于返回以var变量的值命名的变量的值，这话很难理解，其实有些类似C里面的指针，看代码就明白了。

tmp_path=/tmp
path=tmp_path

echo ${!path}
>>> /tmp

shell先将path解析出来，得到tmp_path，再取tmp_path的值，所以得到结果/tmp。

使用eval的方法稍显麻烦：
eval echo \$$path
>>> /tmp

## ${!var@}
