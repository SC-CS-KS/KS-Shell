# 90.Shell 编程技巧

Linux Shell 经典实例解析--Oracle 启动脚本

该篇作为对之前 Linux Shell 常用技巧和高级技巧系列的总结，将以 Oracle 数据库服务器启动脚本为例，逐行进行解释和说明，以帮助我们能够更好的
学习和理解 Shell 脚本的惯用技巧和强大之处。
Oracle 的启动脚本从功能上讲主要分为两个部分，第一部分是初始化各种环境变量，以确认当前 Oracle 服务器的版本，从而进一步确定启动当前服务器
的步骤和具体需要使用的各种 Oracle 工具，第二部分是基于之前判断的结果，读取当前服务器的各种配置信息，之后再通过 Oracle 提供的 Shell 命令完成数
据库的启动工作。
LOGMSG="logger -puser.alert -s "
#1. 信号捕捉，当脚本捕捉到信号 SIGHUP(1)、SIGINT(2)和 SIGQUIT(3)时，执行 exit 命令退出脚本。
trap 'exit' 1 2 3
#2. 如果当前 Shell 环境中指定 ORACLE_TRACE 变量的值为 T，则通过执行 set -x 命令来启动脚本的跟踪功能。
case $ORACLE_TRACE in
T) set -x ;;
esac
SAVE_PATH=/bin:/usr/bin:/etc:${PATH} ; export PATH
SAVE_LLP=$LD_LIBRARY_PATH
#3. $1，即当前脚本的第一个参数，通过查看 init.d 目录下调用该脚本的 Shell 脚本 oracle，可以获悉该参数的值为$ORACLE_HOME 环境变量的值。
ORACLE_HOME_LISTNER=$1
#4. 如果该值不存在，则给出错误提示信息，以及该脚本的合法使用方式。
if [ ! $ORACLE_HOME_LISTNER ] ; then
echo "ORACLE_HOME_LISTNER is not SET, unable to auto-start Oracle Net Listener"
echo "Usage: $0 ORACLE_HOME"
else
LOG=$ORACLE_HOME_LISTNER/listener.log
#5. 导出 ORACLE_HOME 环境变量的值，由于使用了 export 命令，该变量的值在子 Shell 中将同样有效。
export ORACLE_HOME=$ORACLE_HOME_LISTNER
#6. 判断$ORACLE_HOME_LISTNER/bin/tnslsnr 文件是否有可执行权限，如果为真，则通过该命令启动 Oracle 监听，需要注意的是，由于在该行命
令的末尾有一个&符号，这表示该命令将在后台执行。
#7. 在启动监听时，将标准输出以追加的方式重定向到$LOG 变量指向的文件，同时也将标准错误输出也执行到该文件。
if [ -x $ORACLE_HOME_LISTNER/bin/tnslsnr ] ; then
echo "$0: Starting Oracle Net Listener" >> $LOG 2>&1
$ORACLE_HOME_LISTNER/bin/lsnrctl start >> $LOG 2>&1 &
#8. 通过提取 lsnrctl version 的返回信息获取当前 Oracle 服务器的版本，该命令的返回结果为：

#
#
#
#
#
#
#
#
#
LSNRCTL for Linux: Version 11.2.0.1.0 - Production on 14-DEC-2011 17:23:12


Copyright (c) 1991, 2009, Oracle. All rights reserved.


Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=IPC)(KEY=EXTPROC)))
TNSLSNR for Linux: Version 11.2.0.1.0 - Production
TNS for Linux: Version 11.2.0.1.0 - Production
Unix Domain Socket IPC NT Protocol Adaptor for Linux: Version 11.2.0.1.0 - Production
Oracle Bequeath NT Protocol Adapter for Linux: Version 11.2.0.1.0 - Production

#
TCP
/IP NT Protocol Adapter for Linux: Version 11.2.0.1.0 - Production,,

#
The command completed successfully

#9. 在通过 grep 命令对以上结果进行过滤，只输出包含"LSNRCTL for"的行，其结果为：

#
LSNRCTL for Linux: Version 11.2.0.1.0 - Production on 14-DEC-2011 17:25:21

#10.通过 cut 命令对以上结果进行拆分，分隔符为-d 选项指定的空格字符，-f5 表示将输出拆分后的第五个字段，其结果为：

#
11.2.0.1.0

#11.通过 cut 命令对以上结果进行二次拆分，但是这次的分隔符改为点(.)，本次获取的字段为第一个字段，即 11。
VER10LIST=`$ORACLE_HOME_LISTNER/bin/lsnrctl version | grep "LSNRCTL for " | cut -d' ' -f5 | cut -d'.' -f1`
export VER10LIST
else
echo "Failed to auto-start Oracle Net Listener using $ORACLE_HOME_LISTNER/bin/tnslsnr"
fi
fi


ORATAB=/etc/oratab
#12.我想此处代码的本意应为判断/etc/oratab 文件是否以文件的形式存在，然而下面的写法将会使 if 判断永远为真，因此应改为 if [ ! -f $ORATAB ]; then。
-f 用于判断其后的变量是否是为普通文件。如果该文件不存在，脚本将直接退出，退出值为 1，表示失败。需要说明的是，在 Linux 中，通用的规则是返回 0
表示执行成功。
if [ ! $ORATAB ] ; then
echo "$ORATAB not found"
exit 1;
fi


#13. checkversionmismatch 是该脚本的自定义函数，用于判断客户端工具 sqlplus 和 Oracle 服务器之间的版本是否匹配。
checkversionmismatch() {
if [ $VER10LIST ] ; then
#14. 通过 sqlplus -V 获取 sqlplus 的版本，再该通过 grep 命令过滤，仅输出包含 Release 的行，其结果为：

#
SQL*Plus: Release 11.2.0.1.0 Production

#15. 基于以上结果，再通过两次 cut 命令的拆分，最后输出：11。这里 cut 的作用已经在上面的注释中给出。
VER10INST=`sqlplus -V | grep "Release " | cut -d' ' -f3 | cut -d'.' -f1`
#16. 如果服务器的版本($VER10LIST)小于 sqlplus 的版本(VER10INST)，将输出不匹配的提示信息。这里-lt 用于比较数值型变量，表示 A 小于 B。
if [ $VER10LIST -lt $VER10INST ] ; then
$LOGMSG "Listener version $VER10LIST NOT supported with Database version $VER10INST"
$LOGMSG "Restart Oracle Net Listener using an alternate ORACLE_HOME_LISTNER:"
$LOGMSG "lsnrctl start"
fi
fi
}


startinst() {
export ORACLE_SID
#17. 将 oracle 的 bin 目录放置到 PATH 环境变量中，已便于之后的直接调用。
PATH=$ORACLE_HOME/bin:${SAVE_PATH} ; export PATH
#18. LD_LIBRARY_PATH 指出 so 文件所在的路径，这里将 oracle 所依赖的 lib 的路径赋值给该变量，以便 oracle 执行程序在启动时可以找到他们。
LD_LIBRARY_PATH=${ORACLE_HOME}/lib:${SAVE_LLP} ; export LD_LIBRARY_PATH
#19. 下面的变量是 oracle 启动时所需要的服务器实例初始化文件。
PFILE=${ORACLE_HOME}/dbs/init${ORACLE_SID}.ora
SPFILE=${ORACLE_HOME}/dbs/spfile${ORACLE_SID}.ora
SPFILE1=${ORACLE_HOME}/dbs/spfile.ora


echo ""
echo "$0: Starting up database \"$ORACLE_SID\""
date
echo ""
checkversionmismatch


#20. 下面的代码逻辑用于区分当前服务器的版本是否为 V6 或 V7，因为后面的启动逻辑需要为这两个版本做特殊处理。
#21. 首先判断$ORACLE_HOME/bin/sqldba 是否以普通文件的形式存在，如果存在，将通过 sqldba 命令获取版本信息。
VERSION=undef
if [ -f $ORACLE_HOME/bin/sqldba ] ; then
SQLDBA=sqldba
VERSION=`$ORACLE_HOME/bin/sqldba command=exit | awk '
/SQL\*DBA: (Release|Version)/ {split($3, V, ".") ;
print V[1]}'`
#22. 如果版本为 6，则什么也不用做，否则将 VERSION 变量的值统一为 internal。
case $VERSION in
"6") ;;
*) VERSION="internal"
esac
else
#23. 再次判断$ORACLE_HOME/bin/svrmgrl 是否以普通文件的形式存在，如果存在，SQLDBA 的命令将为 svrmgrl，版本为 internal，否则
SQLDBA 命令将指向 sqlplus。需要说明的是，不管是这里的 svrmgrl 还是上面的 sqldba，都是为了向以前版本的兼容，才用 SQLDBA 来动态的表示他们，
事实上，在我们后来的版本中，基本都是使用 sqlplus。
if [ -f $ORACLE_HOME/bin/svrmgrl ] ; then
SQLDBA=svrmgrl
VERSION="internal"
else
SQLDBA="sqlplus /nolog"
fi
fi
#24. 变量 STATUS 为 1 时表示正常值，其它值均表示 oracle 的进程已经拉起。
#25. 先是判断$ORACLE_HOME/dbs/sgadef${ORACLE_SID}.dbf 和$ORACLE_HOME/dbs/sgadef${ORACLE_SID}.ora 这两个文件是否已经
存在。其中${ORACLE_SID}表示变量，shell 在执行时会使用该变量的实际值予以替换，这里之所有用花括号括起${ORACLE_SID}，而不是直接使用
$ORACLE_SID，是因为如果这样使用的话，shell 脚本会将$ORACLE_SID.ora 视为一个变量。
STATUS=1
if [ -f $ORACLE_HOME/dbs/sgadef${ORACLE_SID}.dbf ] ; then
STATUS="-1"
fi
if [ -f $ORACLE_HOME/dbs/sgadef${ORACLE_SID}.ora ] ; then
STATUS="-1"
fi
#26. pmon 是 oracle 的进程监控进程，是 oracle 服务器的核心进程之一。这里通过 ps 命令输出当前 linux 服务器所有进程的列表，再通过 grep 命令
进行过滤，其中-w 选择表示全词匹配，最后再通过一个 grep 命令过滤掉上一个 grep 命令，这里的-v 表示取反，即不包含 grep 的行。
pmon=`ps -ef | grep -w "ora_pmon_$ORACLE_SID" | grep -v grep`
if [ "$pmon" != "" ] ; then
STATUS="-1"
$LOGMSG "Warning: ${INST} \"${ORACLE_SID}\" already started."
fi
#27. 这里是判断数值型变量$STATUS 是否为-1，即进程已经启动。
if [ $STATUS -eq -1 ] ; then
$LOGMSG "Warning: ${INST} \"${ORACLE_SID}\" possibly left running when system went down (system crash?)."
$LOGMSG "Action: Notify Database Administrator."
#28. 既然 oracle 服务器实例已经启动，这里就需要根据 oracle 的版本，用不同的工具和关闭语法 shutdown 已经启动的实例。
case $VERSION in
"6") sqldba "command=shutdown abort" ;;
"internal") $SQLDBA $args <<EOF
connect internal
shutdown abort
EOF
;;
*) $SQLDBA $args <<EOF
connect / as sysdba
shutdown abort
quit
EOF
;;
esac
#29. $?是 shell 脚本的内置变量，用于判断上面关闭 oracle 服务器实例的操作是否成功，0 表示成功，其他值均表示失败。
if [ $? -eq 0 ] ; then
STATUS=1
else
$LOGMSG "Error: ${INST} \"${ORACLE_SID}\" NOT started."
fi
fi
if [ $STATUS -eq 1 ] ; then
#30. 判断$SPFILE、$SPFILE1 或$PFILE 是否存在，-e 表示其后面的变量表示的文件是否存在，-o 表示这几个条件时间的或关系，即 C 语言中的||。
#31. 根本 oracle 的版本，用不同的 oracle 工具启动 oracle 服务器实例，其中不同的工具所使用的语法也不同，这里我们主要需要关注的是 sqlplus。
#32. 在通过 oracle 工具启动服务器时，这里使用了 shell 中的 HERE DOCUMENT，这样可以将一批命令一次性传递给 sqlplus 这样的 oracle 命令。
if [ -e $SPFILE -o -e $SPFILE1 -o -e $PFILE ] ; then
case $VERSION in
"6") sqldba command=startup ;;
"internal") $SQLDBA <<EOF
connect internal
startup
EOF
;;
*) $SQLDBA <<EOF
connect / as sysdba
startup
quit
EOF
;;
esac
#33. 通过判断以上命令的返回值，来判断是否启动成功。
if [ $? -eq 0 ] ; then
echo ""
echo "$0: ${INST} \"${ORACLE_SID}\" warm started."
else
$LOGMSG ""
$LOGMSG "Error: ${INST} \"${ORACLE_SID}\" NOT started."
fi
else
$LOGMSG ""
$LOGMSG "No init file found for ${INST} \"${ORACLE_SID}\"."
$LOGMSG "Error: ${INST} \"${ORACLE_SID}\" NOT started."
fi
fi
}


#34. 用于启动 oracle 的 AMS 实例的函数。
startasminst() {
export ORACLE_SID
#34. $LINE 的值在后面的调用中会给出，该值源自 oratab 文件的输出，其内容为：MyOrcl:/opt/oracle/product/OraHome:Y
#35. 这里使用 awk 命令提取第二个域字段，其中冒号(:)为各个域之间的分隔符，第二个变量($2)为当前实例的 oracle 主目录。
ORACLE_HOME=`echo $LINE | awk -F: '{print $2}' -`
export ORACLE_HOME


#36. 判断$ORACLE_HOME/bin/crsctl 是否有执行权限。
if [ ! -x $ORACLE_HOME/bin/crsctl ]; then
$LOGMSG "$ORACLE_HOME/bin/crsctl not found when attempting to start"
$LOGMSG " ASM instance $ORACLE_SID."
else
#37. 反复执行$ORACLE_HOME/bin/crsctl 命令，直到其执行成功，或在执行 15 次失败后退出脚本。
COUNT=0
$ORACLE_HOME/bin/crsctl check css
RC=$?
#38. 判断 crsctl 命令是否执行成功，如果不等于表示执行失败，则继续执行。
while [ "$RC" != "0" ]; do
#39. 通过 expr 命令，将 COUNT 的变量值加一，这里也可以使用 let 命令，如((COUNT=COUNT+1))。
COUNT=`expr $COUNT + 1`
if [ $COUNT = 15 ] ; then
# 15 tries with 20 sec interval => 5 minutes timeout
$LOGMSG "Timed out waiting to start ASM instance $ORACLE_SID"
$LOGMSG " CSS service is NOT available."
exit $COUNT
fi
$LOGMSG "Waiting for Oracle CSS service to be available before starting "
$LOGMSG " ASM instance $ORACLE_SID. Wait $COUNT."
#40. 每次执行之间都休眠 20 秒。
sleep 20
$ORACLE_HOME/bin/crsctl check css
RC=$?
done
fi
#41. asm 在启动成功后，调用 startinst 函数启动该实例。
startinst
}
在这篇中，只是给出了 oracle 启动脚本中自定义函数的解释，
在下一篇中将进入该脚本的主体部分，其中主体部分的代码将依赖于本篇中给出的函数。

言归正传，该篇将承接上一篇，进入 oracle 启动脚本的主体逻辑部分。
#1. /etc/oratab 脚本的格式如下：

#
#
MyOrcl1:/opt/oracle/product/OraHome:Y
MyOrcl2:/opt/oracle/product/OraHome:N

#该文件的开头处有很多的注释说明，都是以#开头，这些注释需要在后面的处理中被忽略。在有用部分中，每行表示一个 oracle 实例，在同一行中，包含 3
个字段，他们之间用#冒号分隔，第一个字段为 oracle 的 sid，第二个字段为 oracle 实例的主目录，最后一个字段表示本次启动是否拉起该实例，如果为 Y
则拉起，N 则忽略。
#2. cat 以管道的形式，将每行的都输出给 while 循环，作为其输入并赋值给 LINE 变量，如果到了$ORATAB 文件的末尾，while 循环将退出。
cat $ORATAB | while read LINE; do
#3. 如果当前行以#开头后面跟随任意字符，则为注释说明，直接忽略即可。
#4. 如果合法的数据行，用 awk 命令进行切分，并提取第一个域字段，即 oracle 的 sid 值，赋值给变量 ORACLE_SID。如果该变量为空，则直接忽略             ，
continue 命令将回到循环的开头处。
case $LINE in
\#*) ;;
*)
ORACLE_SID=`echo $LINE | awk -F: '{print $1}' -`
if [ "$ORACLE_SID" = '*' ] ; then
ORACLE_SID=""
continue
fi
#5. 这里提取$LINE 变量的最后一个字段，其中 NF 表示 awk 的输入行的字段数量，在本例中 NF 的值为 3，$LINE 的第三个域为状态字段，只有当
该值为 Y 时才拉起该 sid。
if [ "`echo $LINE | awk -F: '{print $NF}' -`" = "Y" ] ; then
#6. 通过 cut 命令截取 ORACLE_SID 的第一个字符，如果其值为加号(+)，则视其为 asm instance。
#7. 这里的 cut 命令可以替换为${ORACLE_SID:0:1}，0 表示从变量$ORACLE_SID 的第一个字符开始，取 1 个字符。
if [ `echo $ORACLE_SID | cut -b 1` = '+' ]; then
INST="ASM instance"
ORACLE_HOME=`echo $LINE | awk -F: '{print $2}' -`
export ORACLE_HOME
LOG=$ORACLE_HOME/startup.log
#8. 通过 touch 命令创建一个日志文件，同时赋予读权限。
touch $LOG
chmod a+r $LOG
echo "Processing $INST \"$ORACLE_SID\": log file $ORACLE_HOME/startup.log"
#9. 调用启动 asm 实例的函数，并将标准输出重定向到刚刚创建的日志文件，同时也将标准错误输出也重定向到该文件。
startasminst >> $LOG 2>&1
fi
fi
;;
esac
done


#10. 如果执行之上的操作失败，则直接退出脚本，退出值为 2。
if [ "$?" != "0" ] ; then
exit 2
fi
#11. 该部分将重新遍历/etc/oratab 文件，并启动数据库实例。该段逻辑中的 shell 技巧和上面的逻辑基本相同，这里仅给出差异部分。
cat $ORATAB | while read LINE; do
case $LINE in
\#*) ;;
*)
ORACLE_SID=`echo $LINE | awk -F: '{print $1}' -`
if [ "$ORACLE_SID" = '*' ] ; then
ORACLE_SID=""
continue
fi
# Proceed only if last field is 'Y'.
if [ "`echo $LINE | awk -F: '{print $NF}' -`" = "Y" ] ; then
#12. 这里和上面不同是，是判断 ORACLE_SID 的第一个字符不为加号(+)，这表示该实例为正常的数据库实例。
if [ `echo $ORACLE_SID | cut -b 1` != '+' ]; then
INST="Database instance"
ORACLE_HOME=`echo $LINE | awk -F: '{print $2}' -`
export ORACLE_HOME
LOG=$ORACLE_HOME/startup.log
touch $LOG
chmod a+r $LOG
echo "Processing $INST \"$ORACLE_SID\": log file $ORACLE_HOME/startup.log"
startinst >> $LOG 2>&1
fi
fi
;;
esac
done


#13. 该段代码逻辑的 shell 应用技巧和之前几段的基本雷同，这里我只是给出技巧上的差异部分。
cat $ORATAB | while read LINE;do
case $LINE in
\#*) ;;
*)
ORACLE_SID=`echo $LINE | awk -F: '{print $1}' -`
if [ "$ORACLE_SID" = '*' ] ; then
ORACLE_SID=""
continue
fi
if [ "`echo $LINE | awk -F: '{print $NF}' -`" = "W" ] ; then
W_ORACLE_SID=`echo $LINE | awk -F: '{print $1}' -`
cat $ORATAB | while read LINE; do
case $LINE in
\#*) ;;
*)
ORACLE_SID=`echo $LINE | awk -F: '{print $1}' -`
if [ "$ORACLE_SID" = '*' ] ; then
ORACLE_SID=""
continue
fi
if [ `echo $ORACLE_SID | cut -b 1` = '+' ]; then
INST="ASM instance"
ORACLE_HOME=`echo $LINE | awk -F: '{print $2}' -`
if [ -x $ORACLE_HOME/bin/srvctl ] ; then
COUNT=0
NODE=`olsnodes -l`
#14. 执行下面的命令，并将其结果用 grep 命令过滤，只保留包含$ORACLE_SID is running 的行，这里$ORACLE_SID 将完成
变量替换。
RNODE=`srvctl status asm -n $NODE | grep "$ORACLE_SID is running"`
RC=$?
#15. 如果执行失败，将继续执行。
while [ "$RC" != "0" ]; do
#16. COUNT=$((COUNT+1))是另外一种进行数值型变量计算的表示方式。
COUNT=$((COUNT+1))
#17. -eq 表示等于$COUNT 等于 5。
if [ $COUNT -eq 5 ] ; then
$LOGMSG "Error: Timed out waiting on CRS to start ASM instance $ORACLE_SID"
exit $COUNT
fi
$LOGMSG "Waiting for Oracle CRS service to start ASM instance $ORACLE_SID"
$LOGMSG "Wait $COUNT."
sleep 60
RNODE=`srvctl status asm -n $NODE | grep "$ORACLE_SID is running"`
RC=$?
done
else
$LOGMSG "Error: \"${W_ORACLE_SID}\" has dependency on ASM instance \"${ORACLE_SID}\""
$LOGMSG "Error: Need $ORACLE_HOME/bin/srvctl to check this dependency"
fi
fi
;;
esac
done # innner while
fi
;;

esac
done # outer while

#18. 在该段代码逻辑中，主要是用于处理/etc/oratab 文件中最后一个字段的值为 W 的情况，它表示所有的 asm 实例均已启动完毕，进入等待状态，此时
将只能启动数据库实例。从 Shell 的应用技巧视角看，该段逻辑和之前的 shell 技巧没有太多差别，这里就不再一一给出注释说明了。
cat $ORATAB | while read LINE; do
case $LINE in
\#*) ;;
*)
ORACLE_SID=`echo $LINE | awk -F: '{print $1}' -`
if [ "$ORACLE_SID" = '*' ] ; then
ORACLE_SID=""
continue
fi
if [ "`echo $LINE | awk -F: '{print $NF}' -`" = "W" ] ; then
INST="Database instance"
if [ `echo $ORACLE_SID | cut -b 1` = '+' ]; then
$LOGMSG "Error: ${INST} \"${ORACLE_SID}\" NOT started"
$LOGMSG "Error: incorrect usage: 'W' not allowed for ASM instances"
continue
fi
ORACLE_HOME=`echo $LINE | awk -F: '{print $2}' -`
export ORACLE_HOME
LOG=$ORACLE_HOME/startup.log
touch $LOG
chmod a+r $LOG
echo "Processing $INST \"$ORACLE_SID\": log file $ORACLE_HOME/startup.log"
startinst >> $LOG 2>&1
fi
;;
esac
done
最后需要说明的是，有兴趣的读者可以继续自行研究$ORACLE_HOME/bin 目录下的另外一个 Shell 脚本(dbshut)，
该脚本主要用于关闭 Oracle 数据库服务器，其代码结构和 Shell 技巧与该脚本(dbstart)极为相似。
