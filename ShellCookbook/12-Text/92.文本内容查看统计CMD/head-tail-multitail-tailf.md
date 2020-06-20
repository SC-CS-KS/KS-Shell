# head/tail/multitail


## head

### head 实例

格式： 
        head [参数]... [文件]...  
----------------------------------------------------------------------------------------
功能：
        head 用来显示档案的开头至标准输出中，默认head命令打印其相应文件的开头10行。 
----------------------------------------------------------------------------------------
参数：
       -q 隐藏文件名
       -v 显示文件名
       -c<字节> 显示字节数
       -n<行数> 显示的行数 
----------------------------------------------------------------------------------------
实例：
        实例1：显示文件的前n行
        命令：head -n 5 log2014.log    也可以写作  head -5 log2014.log
        
        实例2：显示文件前n个字节
        命令：head -c 20 log2014.log

       实例3：文件的除了最后n个字节或者n行以外的内容 
        命令：head -c -32 log2014.log
              head -n -10 log2014.log

### man page

HEAD(1)                                                            User Commands                                                           HEAD(1)

NAME
       head - output the first part of files

SYNOPSIS
       head [OPTION]... [FILE]...

DESCRIPTION
       Print  the first 10 lines of each FILE to standard output.  With more than one FILE, precede each with a header giving the file name.  With
       no FILE, or when FILE is -, read standard input.

       Mandatory arguments to long options are mandatory for short options too.

       -c, --bytes=[-]N
              print the first N bytes of each file; with the leading '-', print all but the last N bytes of each file

       -n, --lines=[-]N
              print the first N lines instead of the first 10; with the leading '-', print all but the last N lines of each file

       -q, --quiet, --silent
              never print headers giving file names

       -v, --verbose
              always print headers giving file names

       --help display this help and exit

       --version
              output version information and exit

       N may have a multiplier suffix: b 512, k 1024, m 1024*1024.

AUTHOR
       Written by David MacKenzie and Jim Meyering.

REPORTING BUGS
       Report bugs to <bug-coreutils@gnu.org>.

COPYRIGHT
       Copyright 2004 Free Software Foundation, Inc.
       This is free software; see the source for copying conditions.  There is NO warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICU-
       LAR PURPOSE.

SEE ALSO
       The  full documentation for head is maintained as a Texinfo manual.  If the info and head programs are properly installed at your site, the
       command

              info coreutils head

       should give you access to the complete manual.

head (coreutils) 5.2.1                                               June 2005                                                             HEAD(1)

## tail

语法：
	
tail命令中各个选项的含义为： 
＋num    从第num行（包含第num行）以后开始显示后面所有行。 
- num    从距文件尾num行处开始显示，如果省略num参数，系统默认值为10。
--------------------------------------------------------------------------------------------------- 
tail -f filename   会把filename里最尾部的内容显示在屏幕上， 并且不断刷新，使你看到最新的文件内容。
--------------------------------------------------------------------------------------------------- 
tail语法格式： 
    tail [ -f ] [ -c Number | -n Number | -m Number | -b Number | -k Number ] [ File ]
     或者 
    tail [ -r ] [ -n Number ] [ File ] 
---------------------------------------------------------------------------------------------------
使用说明：
tail 命令从指定点开始将 File 参数指定的文件写到标准输出。 如果没有指定文件，则会使用标准输入。 

Number 变量<br />指定将多少单元写入标准输出。 Number 变量的值可以是正的或负的整数。 
如果值的前面有 +（加号）， 从文件开头<br />指定的单元数开始将文件写到标准输出。 
如果值的前面有 -（减号）， 则从文件末尾指定的单元数开始将文件写到<br />标准输出。 
如果值前面没有 +（加号）或 -（减号）， 那么从文件末尾指定的单元号开始读取文件。
---------------------------------------------------------------------------------------------------
主要参数：
-b Number 从 Number 变量表示的 512 字节块位置开始读取指定文件。 
-c Number 从 Number 变量表示的字节位置开始读取指定文件。 
-f 如果输入文件是常规文件或如果 File 参数指定 FIFO（先进先出），那么 tail 命令不会在复制了输入文件的最后的指定单元后终止， 
   而是继续从输入文件读取和复制额外的单元（当这些单元可用时）。
   如果没有指定 File 参数，并且标准输入是管道，则会忽略-f 标志。
   tail -f 命令可用于监视另一个进程正在写入的文件的增长。
 
-k Number 从 Number 变量表示的 1KB 块位置开始读取指定文件。 
-m Number 从 Number 变量表示的多字节字符位置开始读取指定文件。使用该标志提供在单字节和双字节字符代码集环境中的一致结果。 
-n Number 从首行或末行位置来读取指定文件， 位置由 Number 变量的符号（+ 或 - 或无）表示，并通过行号 Number 进行位移。 
-r 标志的缺省值是以逆序方式显示整个文件，如果文件大于 20,480 字节，那么-r标志只显示最后的 20,480 字节。
   -r 标志只有与 -n 标志一起时才有效。否则，就会将其忽略。

### tail 实例

tail命令可以输出文件的尾部内容，默认情况下它显示文件的最后十行。
它常用来动态监视文件的尾部内容的增长情况，比如用来监视日志文件的变化。与tail命令对应的是head命令，用来显示文件头部内容。
 
常用参数
格式：tail file
输出指定文件file的尾部内容，默认输出最后十行内容（output the last part of files。Print  the  last 10 lines of each FILE to standard output. ）
 
格式：tail file1 file2 ...
指定多个文件时，会显示每个文件的文件名称，再显示该文件的尾部内容（With more than one FILE, precede each with a header giving the file name.）
 
格式：tail
格式：tail -
不指定文件时，表明从标准输入读取内容，这通常用在管道线后面，把前一个命令的输出作为tail的输入内容（With no FILE, or when FILE is -, read standard input.）
 
格式：tail -n file
格式：tail -n n file
格式：tail --lines=n
显示文件最后n 行，比如tail -20 file就是显示文件最后10行，这个参数可以配合其他参数与使用。注意上面三种格式的斜体n 是实际要显示的行数的数值。
 
注意：tail -n可以显示最后n行的文本内容。那么有没有一种方式显示从n行开始的文本内容，答案是肯定的。
tail -n +4 file表示显示文件file从第4行开始的内容。从1开始计数。
 
格式：tail -f file
动态跟踪文件file的增长情况（output appended data as the file grows），tail会每隔一秒去检查一下文件是否增加新的内容，如果增加就追加在原来的输出后面显示。但这种情况，必须保证在执行tail命令时，文件已经存在。
如果想终止tail -f的输出，按Ctrl+C中断tail程序即可。如果按Ctrl+C不能中断输出，那么可以在别的终端上执行killall tail强行终止。
 
注意：采用tail -f来监控文件变化情况时，在某些情况会不太灵。比如在java应用程序中采用log4j日志时，每隔1个小时生成一个新的日志文件，当前的日志输出在LOG4J.LOG中，当一个小时过去后，log4j会将LOG4J.LOG改名成LOG4J.yyyy-mm-dd-HH的形式。那么这个时候tail -f就不能动态输出新的日志内容了。tail命令本身提供了很多参数，似乎都不能完美的解决这个问题。最后只好编写了一个脚本ftail.sh来跟踪日志，详见《Linux下实时跟踪log4j日志文件的bash脚本 - 增强了tail -f的功能 》。刚才我仔细查看了tail的手册页，发现tail -F就能够做到跟踪这种类型的日志。转念一想，这种需求应该早就被Linux世界的人给满足了的。
 
格式：tail -F file
格式：tail --follow=name --retry file
功能与tail -f file相同，也是动态跟踪文件的变化，不同的是执行此命令时文件可以不存在。
 
以上处理都是针对文本文件的，下面是针对二进制文件的情形。
 
格式：tail -c n file
取文件file的最后n个字节。
 
格式：tail -c +n file
取文件file的第n个字节后的内容。从1开始计数。
使用示例
示例一 输出文件尾部
先使用seq命令输出20个数字保存到1.txt，然后尝试使用tail命令。
# seq 20 >1.txt 
# cat 1.txt 
# tail 1.txt 
# tail -3 1.txt 
# tail -n 3 1.txt 
# tail --lines=3 1.txt 
# tail -n +14 1.txt 
 
示例二 动态跟踪tomcat输出
动态跟踪tomcat输出。
[root@web logs]# tail -f catalina.out 
        at org.apache.catalina.connector.CoyoteAdapter.service(CoyoteAdapter.java:298)
        at org.apache.coyote.http11.Http11Processor.process(Http11Processor.java:852)
        at org.apache.coyote.http11.Http11Protocol$Http11ConnectionHandler.process(Http11Protocol.java:588)
        at org.apache.tomcat.util.net.JIoEndpoint$Worker.run(JIoEndpoint.java:489)
        at java.lang.Thread.run(Thread.java:619)
2010-12-03 13:23:02,236 [http-80-15] DEBUG mhr.roi.MhrManager - MhrGetJobReq={seq=991,job_id='86130469-0006'}
2010-12-03 13:23:02,301 [http-80-15] DEBUG mhr.roi.MhrManager - MhrGetJobRsp={seq=991,result=0(成功),,info={job_id='86130469-0006',employer_id=86130469,employer_name=无锡富士时装有限公司,,,job_title='|570309|',job_title0='文员',job_type=f(全职),issue_time='2010-11-03 00:00:00.0',work_address='1902',work_address0=无锡市,desired_count='1',,,,,,job_desc=大专，1年以上5s管理工作经验，电脑操作熟练。,required_experience=1(一年以上),,,,required_degree=15(大专),,,,,,,valid_days=30,access_count=12,expire_time='2010-12-03 00:00:00.0',job_status=1(过期),,,,,,contact_name=王小姐,contact_number=0510-85550088,remarks='★此职位是从后台导入的
',enter_time='2010-11-03 09:45:11.0',change_time=2010-12-03 02:18:05.0,,job_seq=123201,,required_min_age=22,,,accommodations=面议,serve_principal=wjw12580,job_summary=大专，1年以上5s管理工作经验，电脑操作熟练,,}}
2010-12-03 13:23:02,302 [http-80-15] DEBUG mhr.roi.MhrManager - MhrGetEmployerReq={seq=0,employer_id='86130469'}
2010-12-03 13:23:02,304 [http-80-15] DEBUG mhr.roi.MhrManager - MhrGetEmployerRsp={seq=0,result=0(成功),,info={employer_id='86130469',employer_name=无锡富士时装有限公司,employer_region=1902,employer_address=无锡市滨湖镇山水城科技园8号,,employer_desc=无锡富士时装(集团)有限公司成立于1992年，是中日韩合资企业。主要生产高档针织时装，产品全部外销，连续多年荣获“中国行业500强”、“全国工业重点行业效益十佳企业”、“无锡市百佳企业”等称号。公司总部位于江苏省无锡市滨湖镇山水城科技园，全新的厂房设施，占地面积30亩。公司分别在苏州、泰兴、盐城、徐州设有分厂，集团公司现有职工1500多人，年销售额近3亿元。,,,,,,open_mode=5(系统自动操作),open_time='2010-11-03 09:45:10.0',,,,,contact_name=王小姐,contact_number=0510-85550088,,,,,employer_status=1(已经开通),,,login_password=871386,,agency=false,balance=100.0000,,,,,serve_principal=wjw12580,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,remarks='此帐号由“12580zgz-盐城维克多网络传媒有限公司”导入',enter_time='2010-11-03 09:45:10.0',}}
 
示例三 动态跟踪log4j日志
经过我的试验，发现tail -F功能的强大，它等同于--follow=name --retry。如果你跟踪的文件被移动或者改名后, 你还想继续tail它, 你可以使用这个选项。
tail手册页中关于--retry的说明：keep trying to open a file even if it is inaccessible when tail starts or  if  it  becomes  inaccessible later; useful when following by name, i.e., with --follow=name。 tail命令开始执行时文件不存在或者执行过程中文件不能访问，会不断重试。
关于--follow的说明：-f, --follow[={name|descriptor}] output appended data as the file grows; -f, --follow, and --follow=descriptor are equivalent 。--follow=descriptor表明跟踪的是文件描述符，  --follow=name表明跟踪的是文件名称。 如果文件名称改掉之后，还想继续跟踪原文件名称对应的尾部内容，就得使用-F选项而不是-f选项了。
[root@web imx_server]# tail -F log/IMX.LOG 
14:13:28.892  INFO ImxConnection[6] imx.server.ImxConnection - RX IMX_ACTIVE_TEST{seq=3460,client_id=1291343201649042,presence_status=1(presence_status_online),}
14:13:28.892 DEBUG ImxConnection[6] org.logicalcobwebs.proxool.ImxDB - 006417 (01/02/00) - Connection #9 served
14:13:28.892  INFO ImxConnection[6] imx.dbo.ImxOnlineInfoRow - EXEC SQL UPDATE imx_online_info SET last_active_time = '2010-12-03 14:13:28.0' WHERE account = 'zhy'
14:13:28.894 DEBUG ImxConnection[6] org.logicalcobwebs.proxool.ImxDB - UPDATE imx_online_info SET last_active_time = '2010-12-03 14:13:28.0' WHERE account = 'zhy';  (1 milliseconds)
14:13:28.894 DEBUG ImxConnection[6] org.logicalcobwebs.proxool.ImxDB - 006417 (00/02/00) - Connection #9 returned (now AVAILABLE)
14:13:29.625  INFO ImxConnection[6] imx.server.ImxConnection - RX IMX_ACTIVE_TEST{seq=3461,client_id=1291343201649042,presence_status=1(presence_status_online),}
14:13:29.626 DEBUG ImxConnection[6] org.logicalcobwebs.proxool.ImxDB - 006418 (01/02/00) - Connection #8 served
14:13:29.626  INFO ImxConnection[6] imx.dbo.ImxOnlineInfoRow - EXEC SQL UPDATE imx_online_info SET last_active_time = '2010-12-03 14:13:29.0' WHERE account = 'zhy'
14:13:29.627 DEBUG ImxConnection[6] org.logicalcobwebs.proxool.ImxDB - UPDATE imx_online_info SET last_active_time = '2010-12-03 14:13:29.0' WHERE account = 'zhy';  (0 milliseconds)
14:13:29.653 DEBUG ImxConnection[6] org.logicalcobwebs.proxool.ImxDB - 006418 (00/02/00) - Connection #8 returned (now AVAILABLE)
总结一下：要想跟踪会更名的日志的话，用tail -F而不是tail -f。
 
示例四 处理二进制文件
# seq 20 >1.txt 
# cat 1.txt 
# hexdump -C 1.txt 
00000000  31 0a 32 0a 33 0a 34 0a  35 0a 36 0a 37 0a 38 0a  |1.2.3.4.5.6.7.8.|
00000010  39 0a 31 30 0a 31 31 0a  31 32 0a 31 33 0a 31 34  |9.10.11.12.13.14|
00000020  0a 31 35 0a 31 36 0a 31  37 0a 31 38 0a 31 39 0a  |.15.16.17.18.19.|
00000030  32 30 0a                                          |20.|
00000033
# tail -c 10 1.txt | hexdump -C 
00000000  0a 31 38 0a 31 39 0a 32  30 0a                    |.18.19.20.|
0000000a
# tail -c -10 1.txt | hexdump -C 
00000000  0a 31 38 0a 31 39 0a 32  30 0a                    |.18.19.20.|
0000000a
# tail -c +10 1.txt | hexdump -C 
00000000  0a 36 0a 37 0a 38 0a 39  0a 31 30 0a 31 31 0a 31  |.6.7.8.9.10.11.1|
00000010  32 0a 31 33 0a 31 34 0a  31 35 0a 31 36 0a 31 37  |2.13.14.15.16.17|
00000020  0a 31 38 0a 31 39 0a 32  30 0a                    |.18.19.20.|
0000002a

### man page

TAIL(1)                                                            User Commands                                                           TAIL(1)

NAME
       tail - output the last part of files

SYNOPSIS
       tail [OPTION]... [FILE]...

DESCRIPTION
       Print  the  last 10 lines of each FILE to standard output.  With more than one FILE, precede each with a header giving the file name.  With
       no FILE, or when FILE is -, read standard input.

       Mandatory arguments to long options are mandatory for short options too.

       --retry
              keep trying to open a file even if it is inaccessible when tail starts or if it becomes inaccessible later -- useful only with -f

       -c, --bytes=N
              output the last N bytes

       -f, --follow[={name|descriptor}]
              output appended data as the file grows; -f, --follow, and --follow=descriptor are equivalent

       -F     same as --follow=name --retry

       -n, --lines=N
              output the last N lines, instead of the last 10

       --max-unchanged-stats=N
              with --follow=name, reopen a FILE which has not changed size after N (default 5) iterations to  see  if  it  has  been  unlinked  or
              renamed (this is the usual case of rotated log files)

       --pid=PID
              with -f, terminate after process ID, PID dies

       -q, --quiet, --silent
              never output headers giving file names

       -s, --sleep-interval=S
              with -f, sleep for approximately S seconds (default 1.0) between iterations.

       -v, --verbose
              always output headers giving file names

       --help display this help and exit

       --version
              output version information and exit

       If  the first character of N (the number of bytes or lines) is a '+', print beginning with the Nth item from the start of each file, other-
       wise, print the last N items in the file.  N may have a multiplier suffix: b 512, k 1024, m 1024*1024.

       With --follow (-f), tail defaults to following the file descriptor, which means that even if a tail'ed file is renamed, tail will  continue
       to  track its end.  This default behavior is not desirable when you really want to track the actual name of the file, not the file descrip-
       tor (e.g., log rotation).  Use --follow=name in that case.  That causes tail to track the named file by reopening it periodically to see if
       it has been removed and recreated by some other program.

AUTHOR
       Written by Paul Rubin, David MacKenzie, Ian Lance Taylor, and Jim Meyering.

REPORTING BUGS
       Report bugs to <bug-coreutils@gnu.org>.

COPYRIGHT
       Copyright 2004 Free Software Foundation, Inc.
       This is free software; see the source for copying conditions.  There is NO warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICU-
       LAR PURPOSE.

SEE ALSO
       The full documentation for tail is maintained as a Texinfo manual.  If the info and tail programs are properly installed at your site,  the
       command

              info coreutils tail

       should give you access to the complete manual.

tail (coreutils) 5.2.1                                               June 2005                                                             TAIL(1)

## tailf


### tailf 实例

tailf命令几乎等同于tail -f，严格说来应该与tail --follow=name更相似些。
当文件改名之后它也能继续跟踪，特别适合于日志文件的跟踪（follow the growth of a log file）。
与tail -f不同的是，如果文件不增长，它不会去访问磁盘文件
（It is similar to tail -f but does not access the file when it is not growing.  This has the side effect of not updating the access  time for the file, so a filesystem flush does not occur periodically when no log activity is happening.）。

tailf特别适合那些便携机上跟踪日志文件，因为它能省电，因为减少了磁盘访问嘛
（tailf  is extremely useful for monitoring log files on a laptop when logging is infrequent and the user desires that the hard disk spin down to conserve battery life.）。
tailf命令不是个脚本，而是一个用C代码编译后的二进制执行文件，某些Linux安装之后没有这个命令，本文提供了怎么编译安装tailf命令的方法。
-------------------------------------------------------------------------------------------------------------------------------------------------
常用参数
格式：tailf logfile
动态跟踪日志文件logfile，最初的时候打印文件的最后10行内容。
 
使用示例
示例一 使用tailf命令跟踪日志
# tailf log/WEB.LOG 
        "seq": 5710,
        "clientId": 1291343201649254,
        "presenceStatus": "1"
} }})
16:09:21.324 DEBUG http-80-79 hyjc.cometd.CometdServlet - { "cid": 1291343201649002, "op": "recv", "ncc0": 175}
16:09:21.444 DEBUG http-80-32 hyjc.filter.AccessCounterFilter - uri=/imx/cometd/service
16:09:21.506 DEBUG http-80-79 hyjc.filter.AccessCounterFilter - uri=/imx/cometd/service
16:09:23.239 DEBUG http-80-45 hyjc.cometd.CometdServlet - { "cid": 1291343201649184, "op": "recv", "ncc0": 55}
16:09:23.327 DEBUG http-80-45 hyjc.filter.AccessCounterFilter - uri=/imx/cometd/service
16:09:24.288 DEBUG http-80-145 hyjc.cometd.CometdServlet - { "cid": 1291283017076175, "op": "recv", "ncc0": 82}
16:09:24.339 DEBUG http-80-81 hyjc.cometd.CometdServlet - { "cid": 1291283017076151, "op": "recv", "ncc0": 142}
16:09:24.360 DEBUG http-80-64 hyjc.cometd.CometdServlet - { "cid": 1291343201649090, "op": "recv", "ncc0": 40}
16:09:24.359 DEBUG http-80-143 hyjc.cometd.CometdServlet - { "cid": 1291283017076176, "op": "recv", "ncc0": 66}
16:09:24.878 DEBUG http-80-145 hyjc.filter.AccessCounterFilter - uri=/imx/cometd/service
16:09:24.892 DEBUG http-80-143 hyjc.filter.AccessCounterFilter - uri=/imx/cometd/service
16:09:24.896 DEBUG http-80-64 hyjc.filter.AccessCounterFilter - uri=/imx/cometd/service
16:09:24.906 DEBUG http-80-81 hyjc.filter.AccessCounterFilter - uri=/imx/cometd/service
16:09:25.095 DEBUG http-80-115 hyjc.filter.AccessCounterFilter - uri=/imx/cometd/service
16:09:25.095 DEBUG http-80-115 hyjc.cometd.CometdServlet - msgType IMX_ACTIVE_TEST msgBody {"clientId":1291343201649002,"presenceStatus":"1","seq":385}
16:09:25.095  INFO http-80-115 imx.client.ImxClient - Tx IMX_ACTIVE_TEST{seq=5711,client_id=1291343201649002,presence_status=1(presence_status_online),}
16:09:25.095 DEBUG http-80-115 hyjc.cometd.CometdServlet - { "cid": 1291343201649002, "op": "send", "sent": 0, "rc": 1, "msg": { "mt": "IMX_ACTIVE_TEST", "mb": {
        "seq": 5711,
        "clientId": 1291343201649002,
        "presenceStatus": "1"
} }}
Ctrl+C 
[root@web imx_server]#
 
示例二 编译安装tailf命令
有些Linux版本不带tailf命令的。
# tailf 
-bash: tailf: command not found
到代码搜索网站www.koders.com输入tailf.c就可以搜索到源代码
tailf.c结果页面：http://www.koders.com/default.aspx?s=tailf.c&submit=Search&la=*&li=*
tailf.c源码页面：http://www.koders.com/c/fidB6EFAC156A7B4C4A46B38039C79B4AD34939EED0.aspx?s=tailf#L1
点左上角的download就可下载，也可直接下载本文附件tailf.zip。
# unzip tailf.zip 
Archive:  tailf.zip
  inflating: tailf.c                 
[root@smsgw tailf]# ls 
tailf.c  tailf.zip
[root@smsgw tailf]# gcc -o /usr/bin/tailf tailf.c 
tailf.c:34:17: nls.h: 没有那个文件或目录
tailf.c: In function `tailf':
tailf.c:53: warning: passing arg 2 of `fprintf' makes pointer from integer without a cast
tailf.c: In function `main':
tailf.c:88: `LC_ALL' undeclared (first use in this function)
tailf.c:88: (Each undeclared identifier is reported only once
tailf.c:88: for each function it appears in.)
tailf.c:89: `PACKAGE' undeclared (first use in this function)
tailf.c:89: `LOCALEDIR' undeclared (first use in this function)
tailf.c:93: warning: passing arg 2 of `fprintf' makes pointer from integer without a cast
tailf.c:105: warning: passing arg 2 of `fprintf' makes pointer from integer without a cast
[root@smsgw tailf]#
修改一下tailf.c的源代码。
第34行附近：注释掉头文件，增加宏定义
//#include "nls.h"
#define _(s) s
第89行附近：把原来的代码注释掉
    //setlocale(LC_ALL, "");
    //bindtextdomain(PACKAGE, LOCALEDIR);
    //textdomain(PACKAGE);
看了源代码之后，你是不是发现其实Linux命令其实并不太神秘。
注：本文附件中的tailf.c已经修改成下面的样子。

C代码 
/* tailf.c -- tail a log file and then follow it 
 * Created: Tue Jan  9 15:49:21 1996 by faith@acm.org 
 * Copyright 1996, 2003 Rickard E. Faith (faith@acm.org) 
 * 
 * Permission is hereby granted, free of charge, to any person obtaining a 
 * copy of this software and associated documentation files (the "Software"), 
 * to deal in the Software without restriction, including without limitation 
 * the rights to use, copy, modify, merge, publish, distribute, sublicense, 
 * and/or sell copies of the Software, and to permit persons to whom the 
 * Software is furnished to do so, subject to the following conditions: 
 * 
 * The above copyright notice and this permission notice shall be included 
 * in all copies or substantial portions of the Software. 
 * 
 * THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR 
 * IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, 
 * FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL 
 * THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR 
 * OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, 
 * ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR 
 * OTHER DEALINGS IN THE SOFTWARE. 
 *  
 * less -F and tail -f cause a disk access every five seconds.  This 
 * program avoids this problem by waiting for the file size to change. 
 * Hence, the file is not accessed, and the access time does not need to be 
 * flushed back to disk.  This is sort of a "stealth" tail. 
 */  
  
#include <stdio.h>  
#include <stdlib.h>  
#include <unistd.h>  
#include <malloc.h>  
#include <sys/stat.h>  
//#include "nls.h"  
#define _(s) s  
  
static size_t filesize(const char *filename)  
{  
    struct stat sb;  
  
    if (!stat(filename, &sb)) return sb.st_size;  
    return 0;  
}  
  
static void tailf(const char *filename, int lines)  
{  
    char **buffer;  
    int  head = 0;  
    int  tail = 0;  
    FILE *str;  
    int  i;  
  
    if (!(str = fopen(filename, "r"))) {  
    fprintf(stderr, _("Cannot open \"%s\" for read\n"), filename);  
    perror("");  
    exit(1);  
    }  
  
    buffer = malloc(lines * sizeof(*buffer));  
    for (i = 0; i < lines; i++) buffer[i] = malloc(BUFSIZ + 1);  
  
    while (fgets(buffer[tail], BUFSIZ, str)) {  
    if (++tail >= lines) {  
        tail = 0;  
        head = 1;  
    }  
    }  
  
    if (head) {  
    for (i = tail; i < lines; i++) fputs(buffer[i], stdout);  
    for (i = 0; i < tail; i++)     fputs(buffer[i], stdout);  
    } else {  
    for (i = head; i < tail; i++)  fputs(buffer[i], stdout);  
    }  
    fflush(stdout);  
  
    for (i = 0; i < lines; i++) free(buffer[i]);  
    free(buffer);  
}  
  
int main(int argc, char **argv)  
{  
    char       buffer[BUFSIZ];  
    size_t     osize, nsize;  
    FILE       *str;  
    const char *filename;  
    int        count;  
  
    //setlocale(LC_ALL, "");  
    //bindtextdomain(PACKAGE, LOCALEDIR);  
    //textdomain(PACKAGE);  
  
    if (argc != 2) {  
    fprintf(stderr, _("Usage: tailf logfile\n"));  
    exit(1);  
    }  
  
    filename = argv[1];  
  
    tailf(filename, 10);  
  
    for (osize = filesize(filename);;) {  
    nsize = filesize(filename);  
    if (nsize != osize) {  
        if (!(str = fopen(filename, "r"))) {  
        fprintf(stderr, _("Cannot open \"%s\" for read\n"), filename);  
        perror(argv[0]);  
        exit(1);  
        }  
        if (!fseek(str, osize, SEEK_SET))  
                while ((count = fread(buffer, 1, sizeof(buffer), str)) > 0)  
                    fwrite(buffer, 1, count, stdout);  
        fflush(stdout);  
        fclose(str);  
        osize = nsize;  
    }  
    usleep(250000);     /* 250mS */  
    }  
    return 0;  
}  
   
```bash
# gcc -Wall -o /usr/bin/tailf tailf.c 
# tailf 
Usage: tailf logfile
```

### man page

TAILF(1)                                                     Linux Programmer's Manual                                                    TAILF(1)

NAME
       tailf - follow the growth of a log file

SYNOPSIS
       tailf file

DESCRIPTION
       tailf will print out the last 10 lines of a file and then wait for the file to grow.  It is similar to tail -f but does not access the file
       when it is not growing.  This has the side effect of not updating the access time for the file, so a filesystem flush does not occur  peri-
       odically when no log activity is happening.

       tailf is extremely useful for monitoring log files on a laptop when logging is infrequent and the user desires that the hard disk spin down
       to conserve battery life.

BUGS
       An option could be provided to print out the last n lines instead of the last 10.

AUTHOR
       This program was written by Rik Faith (faith@acm.org) and may be freely distributed under the terms of the X11/MIT License.  There is ABSO-
       LUTELY NO WARRANTY for this program.

SEE ALSO
       tail(1), less(1)

                                                                 13 February 2003                                                         TAILF(1)


## multitail

multitail
multiTail是个用来实现同时监控多个文档、类似tail命令的功能的软件。
他和tail的区别就是他会在控制台中打开多个窗口，这样使同时监控多个日志文档成为可能。
他还可以看log文件的统计，合并log文件，过滤log文件，分屏，……。

官网：http://www.vanheusden.com/multitail/
