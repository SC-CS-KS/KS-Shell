# 20.bashdb 调试实例

bashdb是一个类GDB的调试工具，使用GDB的同学使用bashdb基本无障碍
bashdb可以运行断点设置、变量查看等常见调试操作
bashdb需要单独安装
使用如下：
$ bashdb --debug test.sh            
bash debugger, bashdb, release 4.2-0.8


Copyright 2002, 2003, 2004, 2006, 2007, 2008, 2009, 2010, 2011 Rocky Bernstein
This is free software, covered by the GNU General Public License, and you are
welcome to change it and/or distribute copies of it under certain conditions.


(/home/work/code/test.sh:3):

3:      echo "----------------begin-----------------"
bashdb<0> n  #下一步
----------------begin-----------------
(/home/work/code/test.sh:5):

5:      awk '{sum+=1} END{print sum}' test.sh
bashdb<1> l #列出上下共10行代码
  1:    #!/bin/bash
  2:    
  3:    echo "----------------begin-----------------"
  4:    
  5: => awk '{sum+=1} END{print sum}' test.sh
  6:    
  7:    MAX=3
  8:    for ((i = 0; i < MAX; i++))
  9:    do
 10:            loaddate=`date -d"-$i day" +%Y-%m-%d`
bashdb<2> b 10 #第10行设置断点
Breakpoint 1 set in file /home/work/code/test.sh, line 10.
bashdb<3> c #继续运行
14
Breakpoint 1 hit (1 times).
(/home/work/code/test.sh:10):
10:             loaddate=`date -d"-$i day" +%Y-%m-%d`
bashdb<4> print $i #打印变量值
0
 14:    echo "----------------end-----------------"
