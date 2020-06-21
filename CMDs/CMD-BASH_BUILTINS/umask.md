# umask

## umask 使用

umask
作用：设置新建文件和目录的默认权限。
------------------------------------------------------------------------------------------------------------------------------------------
01  umask和chmod的关系
   umask的值共有4位，分别代表：gid/uid，属主，组权，其它用户权限。一般用的是后3位。
   umask设置的是权限“补码”：如上umask值为022，则对应目录权限为7-0=7，7-2=5，7-2=5，即用777减去umask的相应位上的值。
   而对应的文件权限是用666减去umask的相应位上的值。
   chmod设置的就是目录和文件的直接权限。

02  修改umask的值 
    输入：umask 024，则可直接修改以后新建的目录和文件的默认权限为：553，642。
     
03  umask对应的配置文件
   umask的配置文件为：/etc/profile 或 ~/.profile 或 ~/.bask_profile，修改这些文件里的umask值，则umask的值会长期不变。
------------------------------------------------------------------------------------------------------------------------------------------
umask=022中"022"是八进制的写法，如果换成二进制是000010010

在unix中文件权限是三类用户，三种权限。
三类用户分别是文件所有者user(u)，文件所有者所在主群组group(g)、其它用户others(o)，三种权限分别是起读read(r)、写write(w)、执行execute(x)。

如果一个文件的权限如下：所有者有读写的权限，群组有读和执行权限、其它用户有读权限，可以写成：
rw-r-xr--
其中前三位指明了所有者的权限、中间三位指明了组权限、最后三位指明了其它用户的权限。我们用ls -l可以看到文件权限详情，列出来的是10位，最前一位如果是d表示是子目录。
说回来，如果把这上面9位字母换成二进制数则是：
110101100
如果换成八进制是多少？

因此文件权限可以用9位二进制数表示。umask在英文中是屏蔽的意思，那么
umask=022
指屏蔽文件的022权限，到底屏蔽了什么？将这个八进制数用二进制表示
000010010
换算成字母是
----w--w-
指取消组的写权限、取消其它用户的写权限。
屏蔽这些权限后，剩下什么权限呢？用字母表示是：
rwxr-xr-x
用二进制数表示是：
111101101
你可以对比一下
000010010
可以知道屏蔽前后的换算关系。
------------------------------------------------------------------------------------------------------------------------------------------


umask: 
umask [-p] [-S] [mode]

The user file-creation mask is set to MODE.  
If MODE is omitted, or if `-S' is supplied, the current value of the mask is printed. 
The `-S' option makes the output symbolic; otherwise an octal number is output.
If `-p' is supplied, and MODE is omitted, the output is in a form that may be used as input.  
If MODE begins with a digit, it is interpreted as an octal number, otherwise it is a symbolic mode string like that accepted by chmod(1).

