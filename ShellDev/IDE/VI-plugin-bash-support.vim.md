# VI plugin: bash-support.vim

转载按： 个人认为这篇文章会让经常写shell的兄弟们更加得心应手，以为CU里面已经有了，但是搜了下没有，就转载过来了。

相信没有多少Linuxer不知道vim吧。
我个人认为，它是这个星球上最好的编辑器（EMACS支持者别骂我……），自己也一直使用vim来写程序，曾在这个网站上共享过自己的vimrc。
作为中小级别的程序来说，vim替代其他所有的IDE都完全足够了，而今天要介绍的，就是如何将vim打造成用于bash脚本编程的IDE。
相信我，如果你以前没有使用过vim，试用一下吧，你会爱上它的。

其实仅仅从它自身来讲，Vim已经是最好的shell编辑器之一了。
不过，只要再经过一点点的调整，你就能把Vim从“最好的shell编辑器”变成“最好的shell IDE”。
有两种方法让你实现这个愿望，一种是自己调整，编写功能，估计对于大多数爱好者来说太难了一些。
另一种方法就是安装Fritz Mehner编写的Bash Support插件。

先介绍一下怎么安装这个插件吧，虽然估计大家都知道。
去刚才说的那个网页下载zip压缩包，拷贝到你的~/.vim目录中，解压缩即可。
然后你可以通过编辑~/.vimrc来包含自己的个人详细信息：
    let g:BASH_AuthorName = ‘Your Name’
    let g:BASH_Email = ‘my@email.com’
    let g:BASH_Company = ‘Company Name’

这些变量将用于填充一些工程中的文件头，待会会有详细介绍 。

这个插件支持GUI和文本两种模式的Vim，当然，在GUI模式下会易用性更强（似乎好多插件都是这样的，主要是有菜单栏嘛）。
所以建议最好用 gVim来编写。一旦Bash Support插件被安装上，gVim会自动添加一个叫做Bash的新菜单，其中包括了所有这个插件支持的功能。
让我们来浏览一下其中一些特性，来看看 Bash Support插件是如何让Bash脚本的编写变得轻而易举的。

文件头和注释
如果你喜欢在脚本中广泛使用注释－－我希望编写者都能喜欢－－你肯定会非常乐于使用Bash Support。
它提供了各种各样的功能来让对你的脚本增加注释变得非常简单，你可以通过它实现编程自动化，或者仅仅需要点击几下鼠标或件按键而已。

举个例子，如果你编写的是会正式使用的脚本，会被其他人使用以及维护。那么，在文件开头写上包含有一些基本信息的文件头是一个不错的主意。
这些信息包括脚本名称，用途，描述，注释，作者信息，版权以及其他任何可能对下一个维护这个脚本的用户有用的信息。
Bash Support让你能够轻松做到这点，通过Bash -> Comments -> File Header，然后gVim就会在你的脚本中插入如下文件头：
    #!/bin/bash
    #===============================================================================
    #
    # FILE: test.sh
    #
    # USAGE: ./test.sh
    #
    # DESCRIPTION:
    #
    # OPTIONS: —
    # REQUIREMENTS: —
    # BUGS: —
    # NOTES: —
    # AUTHOR: Joe Brockmeier, jzb@zonker.net
    # COMPANY: Dissociated Press
    # VERSION: 1.0
    # CREATED: 05/25/2007 10:31:01 PM MDT
    # REVISION: —
    #===============================================================================

你需要填充一些具体的信息，不过Bash Support可以自动从你的~/.vimrc中提取作者，公司名以及邮件地址等信息，并自动填充他们，然后自动创建日期。更智能的是，如果你用Vim或者gVim打开后缀名是.sh的新文件，Bash Support可以自动加入文件头。

另外，在编写你的脚本时，你可能需要为一些函数增加注释块，通过Bash -> Comment -> Function来插入如下的文本块：
    #=== FUNCTION ================================================================
    # NAME:
    # DESCRIPTION:
    # PARAMETERS:
    # RETURNS:
    #===============================================================================

然后你只需要填充相关的信息，就可以开始编写程序了。
注释菜单可以让你插入其他不同风格的注释，插入当前日期和时间，以及将选中的代码转换成注释，反之亦然。

程序语句和片段
举个具体点的例子，比如你正在编写脚本。
当你希望往脚本里面添加一个if-else语句的时候，你既可以使用传统的方式，手工输入，又可以使用 Bash Support的预先准备好的语句。
打开Bash -> Statements菜单，你可以看到一长串预先定制好的语句列表，包括while之类的语句。
通过选择，就可以方便地把他们增加到脚本中。
例如，此时你选择while，那么脚本中就会自动添加：

    while _; do
    done

同时，光标会自动处于下划线(_)上面，此时，你所需要做的就是增加测试语句以及你真正希望在while循环中执行的代码。
这也是一个IDE能够帮你做到的一切了。

Bash Support还有更加强大的一面。
假设你的任务是编写大量的脚本，而你有某个函数或者代码片段是会经常在编写过程中重复使用的。
Bash Support就提供了自定义功能，允许你首先高亮选中代码片段或者函数，然后通过Bash -> Statements -> write code snippet菜单保存。
一旦需要提取他们时，通过Bash -> Statements -> read code snippet菜单就可以提取。
除了上述这个办法，你还可以把希望保存的代码片段保存到一个文本文件中，然后将该文件放置到~/.vim/bash- support/codesnippets目录下即可。
这个功能应该说给以编写脚本为职业的朋友提供了很大的帮助。

执行和调试脚本
一旦你的脚本编写完毕，就到了测试和调试的时候了。
通常来讲，你需要退出Vim，将脚本文件设置成可执行属性，然后运行，看看结果，看看有没有什么 bug，之后再回到Vim里去做针对性编辑。
你不觉得这个流程很枯燥繁琐么？ok，现在，Bash Support就能够帮你在Vim环境下测试。

当你想让脚本有可执行权限时，只需要选择Bash -> Run -> make script executable，然后按下Ctrl-F9来保存属性并运行。
另外，还可以通过Bash -> Run -> save来保存，然后通过run运行。
Bash Support还允许你从Vim中调用bash调试器(bashdb) 。
在Ubuntu上似乎没有默认安装，所以你还需要通过apt－get来安装。
安装完毕后，通过Bash -> Run -> start debugger或者直接按F9按键，就能调试脚本了。

Bash Support还拥有很多其他的用于帮助你在Vim中运行和测试脚本的函数，例如一个很有用的特性，语法检查。
当你按下Alt-F9时，Bash Support就会自动帮你检查语法，如果没有语法错误，就会告诉你通过。
一旦有错误，在Vim的屏幕底部会自动出现一个小窗口，里面是语法错误的清单信息。
在这个窗口中高亮选择错误并按下回车键，就能跳转到相应错误出现的语句行上。
具体过程有点类似于Vim下用make调试软件语言，呵呵。

参考书籍一边去
我想很多人都遇到过需要往脚本里面增加某个正则表达式或者别的类似测试语句的时候，而你一定经常不记得这些该死的语法。
难道你对这种情况还没有受够么？不过当你开始使用Bash Support时，这些都将不是问题。因为你将拥有你所希望拥有的所有语法规则或者测试菜单。例如，如果你需要确认一下一个文件是否存在，并且UID是否正确，那么只需要通过Bash -> Tests -> file exists and is owned by the effective UID即可实现，插件能自动在光标后插入合适的测试语句，你接下来的工作就是写出文件名称即可。

如果是想尽快构建一个正则表达式，去Bash菜单下，选择Regex，然后从列表中选择合适的表达式。这对于经常忘记如何表示诸如“1或者0”或者其他正则表达式的朋友来说可以算是非常有用了。Bash Support还包括诸如环境变量，shell选项以及更多的菜单。

热键支持
Vim用户可以利用很多热键来使用Bash Support的特性。虽然并不是像点击菜单那么简单，但是这些热键还是拥有比较容易记忆的逻辑方式。比如，所有注释函数都可以通过\c来访问，因此，如果你希望插入一个文件头，就可以使用\ch，而如果你希望插入日期，则可以使用\cd，如果是一行注释，则是由\cl执行。同理，程序语句是通过\a获得的。\ac表示case语句，\aie表示“if then else”语句，\af则是for语句，等等。说明一下，在线帮助在这方面的内容是错误的，因为里面说语句热键是\s，因此使用时千万注意这一点。

如果用户希望执行命令，则\r可以满足你的要求。例如，想保存并执行脚本，可以用\rr，希望让脚本增加可执行属性，可以使用\re，想进行调试， \rd就能派上用场。这里，我们不会举出所有的热键，你可以通过输入:help bashsupport-usage-vim来获得详细的帮助信息，这点估计Vim用户都知道。
总结

事实上，我们在这里只是介绍了Bash Support一小部分的功能。以后，当你需要编写大量脚本时，可以试一试安装了Bash Support插件的Vim，它确实为脚本的编写提供了极大的帮助。
