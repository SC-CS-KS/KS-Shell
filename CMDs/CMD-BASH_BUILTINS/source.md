# source/.

读取和执行包含在指定文件中的命令，并返回。
如：
当有Shell函数在多个脚本中使用时，正确方式是将它放在库文件中，在以点号命令来读取它们。

如指定的文件不含斜杠，则Shell会查找$PATH，以找到该文件。
该文件无需可执行，只要可读即可。

注意： 
任何读进来的文件都是在当前Shell下执行，因此，变量赋值，函数定义，以及cd的目录变更，都会有效。
这和简单的执行各种的Shell脚本是很不相同的，后者在个别的进程中执行，不对当前的Shell产生任何影响。
---------------------------------------------------------------------------------------------------------------------------
.: 
. filename [arguments]

Read and execute commands from FILENAME and return.  

The pathnames in $PATH are used to find the directory containing FILENAME.  
If any ARGUMENTS are supplied, they become the positional parameters when FILENAME is executed.
---------------------------------------------------------------------------------------------------------------------------

source: 
source filename [arguments]
Read and execute commands from FILENAME and return.  
The pathnames in $PATH are used to find the directory containing FILENAME.  
If any ARGUMENTS are supplied, they become the positional parameters when FILENAME is executed.
----------------------------------------------------------------------------------------------------------------------------------------
用法：
　　source FileName
----------------------------------------------------------------------------------------------------------------------------------------
作用:
在当前bash环境下读取并执行FileName中的命令。

注：该命令通常用命令“.”来替代。如：source .bash_rc 与 . .bash_rc 是等效的。
source命令(从 C Shell 而来)是bash shell的内置命令。点命令，(从Bourne Shell而来)是source的另一名称。
同样的，当前脚本中配置的变量也将作为脚本的环境，source(或点)命令通常用于重新执行刚修改的初始化文档，如 .bash_profile 和 .profile 等等。
例如：
假如在登录后对 .bash_profile 中的 EDITER 和 TERM 变量做了修改，则能够用source命令重新执行 .bash_profile 中的命令而不用注销并重新登录。

比如您在一个脚本里export KKK=111 ，假如您用./a.sh执行该脚本，执行完毕后，您运行 echo $KKK ，发现没有值。
假如您用source来执行，然后再echo ，就会发现KKK=111。
因为调用./a.sh来执行shell是在一个子shell里运行的，但是 source不同的就是在本shell中执行的。
----------------------------------------------------------------------------------------------------------------------------------------
实例：
source命令的一个妙用，在编译核心时，常常要反复输入一长串命令。
　　make mrproper
　　make menuconfig
　　make dep
　　make clean
　　make bzImage
　　.......
这些命令既长，又繁琐。而且有时候容易输错，浪费你的时间和精力。
如果把这些命令做成一个文件，让它自动按顺序执行，对于需要多次反复编译核心的用户来说，会很方便。

用source命令可以办到这一点。它的作用就是把一个文件的内容当成是shell来执行。
先在/usr/src/linux- 2.4.20目录下建立一个文件，取名为make_command，在其中输入如下内容：
　　make mrproper &&
　　make menuconfig &&
　　make dep &&
　　make clean &&
　　make bzImage &&
　　make modules &&
　　make modules_install &&
　　cp arch/i386/boot/bzImge /boot/vmlinuz_new &&
　　cp System.map /boot &&
　　vi /etc/lilo.conf &&
　　lilo -v
文件建立好之后，以后每次编译核心，只需要在/usr/src/linux-2.4.20下输入source make_command就行了。
这个文件也完全可以做成脚本，只需稍加改动即可。这里主要是让大家理解source的用法。
如果你用的不是lilo来引导系统，可以把最后两句话去掉。配置你自己的引导程序来引导新内核。

shell编程中的命令有时和C语言是一样的。&&表示与，||表示或。
把两个命令用&&联接起来，如 make mrproper && make menuconfig ，表示要第一个命令执行成功才能执行第二个命令。
对执行顺序有要求的命令能保证一旦有错误发生，下面的命令不会盲目地继续执行。
----------------------------------------------------------------------------------------------------------------------------------------
