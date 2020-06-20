# 05.LANG/NLS_LANG

LANG
是针对Linux系统的语言、地区、字符集的设置。当前locale的默认名称，其它的LC_*变量会覆盖其值。
对linux下的应用程序有效，如控制date命令输出为英文还是中文等。
#echo $LANG
POSIX
---------------------------------------------------
NLS_LANG
是针对Oracle的语言、地区、字符集的设置。
对oracle中的工具有效。
------------------------------------------------------------------------------------------------------------- 
$export LANG=zh_CN.GB2312  
$date  
2012年 11月 27日 星期二 16:20:35 CST  
显示是中文界面。
------------------------------------------------------------
$export NLS_LANG=AMERICAN_AMERICA.ZHS16GBK  
$sqlplus / as sysdba  
SQL*Plus: Release 10.2.0.4.0 - Production on Tue Nov 27 16:19:03 2012  
Copyright (c) 1982, 2007, Oracle.  All Rights Reserved.  
Connected to:  
Oracle Database 10g Enterprise Edition Release 10.2.0.4.0 - 64bit Production  
With the Partitioning, Real Application Clusters, OLAP, Data Mining  
and Real Application Testing options  
SQL> select sysdate from dual;  

SYSDATE  
------------  
27-NOV-12  
显示的是英文环境，但只对ORACLE程序生效。
------------------------------------------------------------------------------------------------------------- 
Linux系统中关于环境变量的LANG设置主要有下列项目：
# locale  
LANG=zh_CN.gb2312  
LC_CTYPE="zh_CN.gb2312"  
LC_NUMERIC="zh_CN.gb2312"  
LC_TIME="zh_CN.gb2312"  
LC_COLLATE="zh_CN.gb2312"  
LC_MONETARY="zh_CN.gb2312"  
LC_MESSAGES="zh_CN.gb2312"  
LC_PAPER="zh_CN.gb2312"  
LC_NAME="zh_CN.gb2312"  
LC_ADDRESS="zh_CN.gb2312"  
LC_TELEPHONE="zh_CN.gb2312"  
LC_MEASUREMENT="zh_CN.gb2312"  
LC_IDENTIFICATION="zh_CN.gb2312"  
LC_ALL=  
 
这里LC_ALL没有设置，如果它设置了，上面所有的设置都无效的，系统会读取LC_ALL。
locale -a 查看本地字符集
locale -m 查看所有支持的字符集
--------------------------------------------------------------------------------------
在Oracle数据库中查NLS_LANG设置：
SQL> SELECT *  
  2    FROM DATABASE_PROPERTIES  
  3   WHERE PROPERTY_NAME IN  
  4         ('NLS_LANGUAGE', 'NLS_TERRITORY', 'NLS_CHARACTERSET');  
PROPERTY_NAME                  PROPERTY_VALUE                 DESCRIPTION  
------------------------------ ------------------------------ --------------------  
NLS_LANGUAGE                   AMERICAN                       Language  
NLS_TERRITORY                  AMERICA                        Territory  
--------------------------------------------------------------------------------------
SUSE上修改语言设置与RedHat有所不同，RedHat上修改/etc/sysconfig/i18n文件，但是SUSE上却是修改/etc/sysconfig/language文件，其余步骤基本相同。
可以先用locale -a查看系统上有哪些可以设置的语言。然后修改i18n文件或language文件，保存后退出，重启机器。
1. 修改 /etc/sysconfig/language 文档
将文档中 RC_LANG 配置为 "zh_CN.GBK"
2. 运行SuSEconfig
SuSEconfig 根据 /etc/sysconfig/ 目下文件来设定系统。该命令在SuSe12已经废除。
--------------------------------------------------------------------------------------
 
