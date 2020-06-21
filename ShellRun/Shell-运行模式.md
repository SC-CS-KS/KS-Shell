# 16.Shell 运行模式

Bash有几种不同的运行模式：
login shell与non-login shell
interactive shell与non-interactive shell（比如执行shell脚本）。

两种分类方法是交叉的。
即一个login shell可能是一个interactive shell，也可能是个non-interactive shell。


交互式shell和非交互式shell、登录shell和非登录shell 是两个不同的维度来划分的，一个是是否交互式，另一个是是否登录。


## 01.交互式 Shell

01.!$  
是一个特殊的环境变量，它代表了上一个命令的最后一个字符串。

02.sudo !! 
以root的身份执行上一条命令，!! 代表上一个命令。

03.cd –
回到上一次的目录。
场景举例：当前目录为/home/a，用cd ../b切换到/home/b。这时可以通过反复执行cd –命令在/home/a和/home/b之间来回方便的切换。
（陈皓注：cd ~ 是回到自己的Home目录，cd ~user，是进入某个用户的Home目录）

04.^old^new
替换前一条命令里的部分字符串。
场景：echo "wanderful"，其实是想输出echo "wonderful"，只需要^a^o就行了，对很长的命令的错误拼写有很大的帮助。
（陈皓注：也可以使用 !!:gs/old/new）

05.在命令行前加空格，该命令不会进入history里。

06.man ascii  显示ascii码表。  

## interactive shell/non-interactive shell

交互式shell和非交互式shell（interactive shell and non-interactive shell）
交互式模式就是在终端上执行，shell等待你的输入，并且立即执行你提交的命令。
这种模式被称作交互式是因为shell与用户进行交互。
这种模式也是大多数用户非常熟悉的：
登录、执行一些命令、退出。当你退出后，shell也终止了。

shell也可以运行在另外一种模式：
非交互式模式，以shell script(非交互)方式执行。
在这种模式 下，shell不与你进行交互，而是读取存放在文件中的命令,并且执行它们。
当它读到文件的结尾EOF，shell也就终止了。
可以通过打印“$-”变量的值（代表着当前shell的选项标志），查看其中的“i”选项（表示interactive shell）来区分交互式与非交互式shell。

```bash
# echo $-
himBH
# ./test.sh
echo $-
hB
```

## login shell/non-login shell

在下列情况下，可以获得一个login shell：
1.登录系统时获得的顶层shell，无论是通过本地终端登录，还是通过网络ssh登录。这种情况下获得的login shell是一个交互式shell。
2.在终端下使用--login选项调用bash，可以获得一个交互式login shell。
3.在脚本中使用--login选项调用bash（比如在shell脚本第一行做如下指定：#!/bin/bash --login），此时得到一个非交互式的login shell。
4.使用"su -"切换到指定用户时，获得此用户的login shell。如果不使用"-"，则获得non-login shell。

login shell与non-login shell的主要区别在于它们启动时会读取不同的配置文件，从而导致环境不一样。
login shell启动时首先读取/etc/profile全局配置，然后依次查找~/.bash_profile、~/.bash_login、~/.profile三个配置文件，并且读取第一个找到的并且可读的文件。login shell退出时读取并执行~/.bash_logout中的命令。

交互式的non-login shell启动时读取~/.bashrc资源文件。
非交互式的non-login shell不读取上述所有配置文件，而是查找环境变量BASH_ENV，读取并执行BASH_ENV指向的文件中的命令。

如果使用命令"sh"调用bash，bash会尽可能保持向后兼容。
作为login shell启动时，bash依次读取/etc/profile和~/.profile配置文件，作为non-login shell启动时，bash读取环境变量ENV指向的文件。

通常我们要定制一些配置时，将配置写在~/.bashrc中，然后在~/.bash_profile中读取~/.bashrc。
这样可以保证login shell和交互式non-login shell得到相同的配置。
至于/etc/profile就不要轻易去改啦，毕竟会影响系统全局的配置。

下面做个简单的实验来验证上面的描述。在~/.bash_profile中设置如下变量：
lshell="login shell will see this message"
分别启动一个交互式non-login shell和交互式login shell，查看lshell变量：

```bash
$ bash
$ echo $lshell

$ exit
exit
$ bash --login
$ echo $lshelll
login shell will see this message
$ exit
logout
```

可见non-login shell并没有读取~/.bash_profile，login shell读取了，与上面的描述相符。

登录shell：
是需要用户名、密码登录后才能进入的shell（或者通过”–login”选项生成的shell）。
非登录shell：
当然就不需要输入用户名和密码即可打开的Shell。
例如：直接命令“bash”就是打开一个新的非登录shell，在Gnome或KDE中打开一个“终端”（terminal）窗口程序也是一个非登录shell。
执行exit命令，退出一个shell（登录或非登录shell）；
执行logout命令，退出登录shell（不能退出非登录shell）。

```bash
# bash --login
# logout
# bash --login
# exit
logout
 
#bash
master@jay-intel:~$ logout
bash: logout: not login shell: use `exit'
# exit
exit
```

## 总结

Login Shell：
当init3时，mingetty spawn出登录提示符的时候，你输入帐号和密码进入了，出现了shell提示符，这个过程就是登录shell。

Interactive Shell：
接下来你得到了一个像“root@host />”这样的提示符，你输入什么，它就解释出什么，这就是交互式shell。
你在当前shell又fork了一个 shell（bash)，这个bash也是交互式shell。
通常情况下interactive shell是login shell派生出来的。

Non Interactive Shell：
你需要写一个bash script，用外部shell执行“bash script.sh”命令。
它（bash）从可以第一条命令执行到最后一条然后退出，不与你进行任何交互，它就是非交互式shell。

交互式非登录shell：
例如你在Gnome图形界面中打开“终端”出来的那种窗口程序。
和登录shell相比，它是“非登录”的，你并不需要输入用户名和密码，和非交互式shell相比，这是“交互式”的，就像你说的那它“你输入什么，它就解释什么”。

对于Bash来说，登录shell（包括交互式登录shell和使用“–login”选项的非交互shell）。
它会首先读取和执行/etc/profile全局配置文件中的命令，然后依次查找~/.bash_profile、~/.bash_login 和 ~/.profile这三个配置文件。
读取和执行这三个中的第一个存在且可读的文件中命令。除非被“–noprofile”选项禁止了。

在非登录shell里，只读取 ~/.bashrc （和 /etc/bash.bashrc、/etc/bashrc ）文件。
不同的发行版里面可能有所不同，如RHEL6.3中非登录shell仅执行了“~/.bashrc”文件（没有执行/etc/bashrc）。
而KUbuntu10.04中却依次执行了/etc/bash.bashrc 和 ~/.bashrc 文件。
对于这些规则，可以直接在相应的配置文件中加一些echo命令来验证其真实性。
