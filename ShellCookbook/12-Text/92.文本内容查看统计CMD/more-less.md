# more/less

## more

more
使用方式：
more [-dlfpcsu] [-num] [+/pattern] [+linenum] [fileNames..]    一次可以有多个文件对象。
------------------------------------------------------------------------
说明：以一页一页的显示，方便使用者逐页阅读。
------------------------------------------------------------------------
子命令：
按空白键（space）就往下一页显示
按 b 键就会往回（back）一页显示
按Enter键会显示下一行的内容
按 v 键使用vi编辑器来编辑查看文档
按 h 键查看命令说明文件
------------------------------------------------------------------------
参数：
-num     一次显示的行数 
-p       显示下一屏之前先清屏。
-c       作用同- p基本一样。不同的是先显示内容再清除其他旧资料 。
-d       在每屏的底部显示更友好的提示信息：
         -- More -- （XX%）[Press space to contiune , ‘q’ to quit . ]
         而且若用户输入了－个错误命令则显示出错信息，而不是简单地鸣响终端。
-l       不处理 Ctrl+l （换页符）。如果没有给出这个选项，则more命令在显示了一个包含有 Ctrl+l 字符的行后将暂停显示，并等待接收命令。
-s       文件中连续的空白行压缩成一个空白行显示。
-f       计算行数时，以实际上的行数，而非自动换行过后的行数（有些单行字数太长的会被扩展为两行或两行以上）
-u       不显示下引号 （根据环境变数 TERM 指定的 terminal 而有所不同）
+/       在每个档案显示前搜寻该字串（pattern），然后从该字串之后开始显示 
+num     从第 num 行开始显示 

### 90.Code: more v0.1

```c
/* my_more.c - version 0.1  
 * read and print 24 lines then pause for a few special commands
 * by chenguang02@baidu.com
 * 2013/11/25
 */

#include <stdio.h>
#include <errno.h>
#include <stdlib.h>

#define PAGE_ROW_DISPLY 24
#define PAGE_LINE_DISPLAY 512

void do_more(FILE *);
int get_cmd(FILE *);

int main(int ac, char *av[])
{
    FILE *fp;
    if (1 == ac)
        do_more(stdin);
    else {
        while (--ac) { /*  more than one file, deal with one by one */
            if ((fp = fopen(*++av, "r")) != NULL) {
                do_more(fp);
                fclose(fp);
            } else {
                perror("Open file :");
                exit(1);
            }
        }
    }
    return 0;
}

void do_more(FILE * fp)
/*  
 *  read PAGE_LINE_DISPLAY, then call get_cmd() for further instrustions 
 */
{
    char line_char_arr[PAGE_LINE_DISPLAY];
    int num_of_lines = 0;
    int replay;
    FILE *fp_tty;    /* 解决通过管道传递数据给程序的问题，与用户交互的输入不能再从标准输入读取  */
    fp_tty = fopen("/dev/tty", "r");  /* 直接读取键盘的设备文件 */
    while (fgets(line_char_arr, PAGE_LINE_DISPLAY, fp) != NULL) {  /* input  */
    /* 书上这里的只判断等于，我加了大于的情况，用于处理输入回车的情况 */
    if (num_of_lines >= PAGE_ROW_DISPLY) {  /* full screen ? */
        replay = get_cmd(fp_tty);  /* ask user */
        if (0 == replay) { /* input 'q', means quit */
            exit(1);
        }
        if (PAGE_ROW_DISPLY == replay) { /* input space ,dispay next page  */
            num_of_lines = 0;  /* reset page line count  */
        }
    } 
    if (fputs(line_char_arr, stdout) == EOF) /* ouput a line  */
        exit(1);
        num_of_lines++; /* count current page lines */
    }
}

int get_cmd(FILE * fp_tty)
/*
 * print message, wait for response,
 * q means quit, space means next page, CR means next line
 * */
{
    int char_from_input;
    printf("\033[7m more ?\033[m");   /* reverse on a vt100 */
    fflush(stdout);  /* flush stdout buffer */
/* 
 *  getc 或getchar 在读取用户输入时，回车符会被读入缓冲区，被当成函数的输入。
 *  这种情况下，难以区分是用户主动输入的回车，还是为了提交输入字符而输入的回车符。
 *  解决方案是：为键盘输入，重新设定两个字符大小的缓冲区，输入一次后，把缓冲区第二个空间置为对程序无影响的字符
 */
    char get_char_buffer[2];
    setbuf(fp_tty, get_char_buffer);
    while ((char_from_input = getc(fp_tty)) != EOF) {  /* get response */
    get_char_buffer[1] = '\0';
    switch (char_from_input) {
        case 'q':  /* q stand for quit */
            return 0;
        case ' ':    /* space stand for display next page */
            return PAGE_ROW_DISPLY;
        case '\n':   /* CR stand for display next line */
            return 1;
        }
    }
    return 0;
}
```

仍需解决的问题：
01.如何不用回车，程序就能得到输入？而且不让输入的字符展示出来？

02.如何计算和展示百分比？
要知道百分比就必须知道文件的大小，这些信息操作系统是提供的，需要适当的系统调用获取。

03.已经展示过的页，如果去掉 “more ?”?

04.如果根据终端类型来展示每页的行数？使程序适应各种类型的终端？
3和4的问题，需要控制和调整终端参数。

## 30.less

less
说明：
原为弥补more不能向前翻页查看设计。
可以使用 [pageup] [pagedown] 等按键的功能来往前往后翻看文件，拥有更多的搜索功能，less 在查看之前不会加载整个文件。
------------------------------------------------------------------------ 
子命令：
   /字符串         向下搜索“字符串”的功能
   ?字符串         向上搜索“字符串”的功能
   n               重复前一个搜索（与 / 或 ? 有关）
   N               反向重复前一个搜索（与 / 或 ? 有关）
   b               向后翻一页
   d               向后翻半页
   u               向前滚动半页
   h               显示帮助界面
   Q               退出less 命令
   y               向前滚动一行
   空格键          滚动一页
   回车键          滚动一行
   [pagedown]      向下翻动一页
   [pageup]        向上翻动一页
------------------------------------------------------------------------
参数：
   -b              <缓冲区大小> 设置缓冲区的大小
   -e              当文件显示结束后，自动离开
   -f              强迫打开特殊文件，例如外围设备代号、目录和二进制文件
   -g              只标志最后搜索的关键词
   -i              忽略搜索时的大小写
   -m              显示类似more命令的百分比
   -N              显示每行的行号
   -o              <文件名> 将less 输出的内容在指定文件中保存起来
   -Q              不使用警告音
   -s              显示连续空行为一行
   -S              行过长时将超出部分舍弃
   -x <数字>       将“tab”键显示为规定的数字空格

### 32.less 颜色设置

将下面的代码拷贝到~/.bash_login中，可以让man有颜色

export LESS_TERMCAP_mb=$'\E[01;31m'
export LESS_TERMCAP_md=$'\E[01;31m'
export LESS_TERMCAP_me=$'\E[0m'
export LESS_TERMCAP_se=$'\E[0m'
export LESS_TERMCAP_so=$'\E[01;44;33m'
export LESS_TERMCAP_ue=$'\E[0m'
export LESS_TERMCAP_us=$'\E[01;32m'

## 指定less为默认阅读器
export PAGER=less


### less 技巧

在less浏览时编辑文件  
要编辑一个正在用less浏览的文件，可以按下v，你就可以用变量 $EDITOR 所指定的编辑器来编辑了：  

```bash
less foo.html
## 按下v键来编辑文件 ##
## 退出编辑器后，你可以继续用less浏览了 ##
```


