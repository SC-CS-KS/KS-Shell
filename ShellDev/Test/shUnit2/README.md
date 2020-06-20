# shUnit2

shUnit2使用指南
								wengjy
目录
shUnit2简介	2
shUnit2入门	2
shUnit2全面了解	4
测试函数	4
断言	5
skipping	6
回调函数	8

shUnit2简介
shUnit2 是针对shell脚本的单元测试框架，一开始是为了对log4sh项目进行单元测试才开发的，大家可以从log4sh项目里找到它的单元测试用例。目前该单元测试框架支持的shell有：
1)	Bourne Shell (sh)
2)	BASH - GNU Bourne Again SHell (bash)
3)	DASH (dash)
4)	Korn Shell (ksh)
5)	pdksh - Public Domain Korn Shell (pdksh)
6)	zsh - ZSH (zsh) (since 2.1.2) 

shUnit2入门
下载shUnit2源码包（http://sourceforge.net/projects/shunit2），
解压后可以在src/shell目录下看到shunit2文件，该文件即是这套测试框架的全部代码。

我们举个简单的例子来看看shUnit2该如何使用，
首先编写一个小的bash脚本，该脚本提供了一个将第一个参数跟第二个参数相加并显示结果的简单函数add：
#!/bin/bash

add()
{
echo $(($1+$2))
return 0
}

接下来编写测试脚本add_test.sh：
#!/bin/bash

testAdding()
{
result=`add 1 2`
assertEquals \
"the result of '${result}' was wrong" \
"${result}" 3
}

oneTimeSetUp()
{
. ./add.sh
}

# load and run shUnit2
. ./shunit2

之后把shunit2复制到当前目录下，执行add_test.sh脚本，便可以得到单元测试结果了：
#
# Performing tests
#
testAdding

#
# Test report
#
tests passed:     1 100%
tests failed:     0   0%
tests skipped:    0   0%
tests total:      1 100%


可以看到最后还将会得到一个测试报告。我们主要来看看add_test.sh，
首先编写一个测试函数testAdding， 函数运行add函数，并检查结果是否符合预期。
在oneTimeSetUp函数里我们加载add.sh脚本，注意add.sh脚本应该是个函数集合（即无主函数），
该函数可以看成是shunit2的一个回调函数，它将在测试一开始运行一次，所以应当在里头加载我们自己的add.sh脚本。
最后执行shUnit2开始测试。

这里需要注意的是，add.sh 跟 shUnit2都必须通过dot命令来加载（或着说是运行）。
这样才能在当前环境（该例是add_test.sh）下使用脚本里定义的函数或者变量。

至此，我们已经看到了如何使用shUnit2进行简单的测试，下面将进行更全面的介绍。

shUnit2全面了解
在这一节，我们将仍然以最简单的例子来了解shUnit2提供的功能。

	测试函数
从刚才的例子，我们知道了如何编写测试函数，可shUnit2又是如何知道哪些函数是测试函数的？
shUnit2框架默认情况下将测试脚本里所有以test开头的函数作为测试函数。
但如果项目里的函数必须有自己的前缀，测试函数也不例外，这时候就需要自定义了。

shUnit2为我们提供了suite()跟suite_addTest()两个函数来完成注册测试函数的功能。
suite函数个是回调函数，需要我们自己编写，我们在suite里通过调用suite_addTest()来添加测试函数：
#!/bin/bash

suite()
{
        suite_addTest my_test
        suite_addTest my_test2
}

my_test()
{
        :
}

my_test2()
{
        :
}

# load and run shUnit2
. ./shunit2

当默认命名规则不能满足你的需求时，你需要它。

	断言
接下来看看断言（assert），这跟C语言里的断言功能类似，当表达式不成立时显示警告信息。
上节已经在add_unit.sh里使用了 assertEquals来比对add函数的返回值。shUnit2提供了这么几个断言函数：

函数原型
描述
assertEquals ([message],expected,actual)
测试预期值(expected)跟真实值(actual)是否相等
assertNull ([message],value)
测试value是否为空
assertNotNull ([message],value)
测试value是否非空
assertSame ([message],expected,actual)
测试预期值(expected)跟真实值(actual)是否相等,从功能上等同于assertEquals
assertNotSame ([message],unexpected,actual)
测试预期值(expected)跟真实值(actual)是否不相等
assertTrue ([message],condition)
测试条件(condition)是否成立
assertFalse([message],condition)
测试条件(condition)是否不成立

这里的[message]参数都是可选的，若提供了[message]参数，将在断言失败时显示该参数值。
比如assertTrue "test failed" "[ -r '/non/existant/file' ]"，当/non/existant/file不可读时，将显示测试失败。

当断言失败时，除了显示[message]参数，还会递增失败用例数（最终输出的测试报告处可以看到）。
#
# Performing tests
#
testAdding
ASSERT:the result of '3' was wrong expected:<3> but was:<4>

#
# Test report
#

tests passed:     0   0%
tests failed:     1 100%
tests skipped:    0   0%
tests total:      1 100%

shUnit2还提供一组fail函数，主要用来强制失败，实际使用情况很少，这里不再列举。
下面我们来看看编写通用测试用例时如何在不同平台下屏蔽差异。

	skipping
还是一开始的add.sh, 这回我们新添了一个使用expr方法的add函数：
#!/bin/bash

add()
{
        echo $(($1+$2))
        return 0
}

add_by_expr()
{
        expr $1 + $2
        return 0
}


在非bash环境下，将不能使用$(())方式来进行数值计算。添加这一接口将使我们的add脚本更加通用。
但这也为编写测试脚本带来一个新问题，当在非bash环境下时如何屏蔽对add函数的测试，只测试add_by_expr呢？

shUnit2提供了skipping这类函数来解决这类问题，主要有三个函数：
函数原型
描述
startSkipping()

开始忽略后续的assert跟fail类函数
endSkipping()

停止忽略assert跟fail类函数
isSkipping()
查看当前是否处于skipping状态

当我们执行startSkipping后，当出现assert或者是fail这两类函数，将不做检查，最终在测试报告里将归结到skipped一类。
endSkipping结束skipping状态。结合个例子应该能更好理解，我们为上述新的add.sh编写新的测试脚本：
#!/bin/bash

testAdding()
{
  result=`add_by_expr 1 2`
  assertEquals \
      "the result of '${result}' was wrong" \
      "${result}" 3

  [ -z "${BASH_VERSION:-}" ] && startSkipping

  result=`add 1 2`
  assertEquals \
      "the result of '${result}' was wrong" \
      "${result}" 3
}

oneTimeSetUp()
{
  # load include
  . ./add.sh
}

# load and run shUnit2
. ./shunit2

一开始进行了add_by_expr函数的测试，当BASH_VERSION变量为空时，将跳过add函数的测试。

先使用bash运行测试脚本add_test.sh
#
# Performing tests
#
testAdding

#
# Test report
#
tests passed:     2 100%
tests failed:     0   0%
tests skipped:    0   0%
tests total:      2 100%


再使用非bash运行测试脚本，这里我使用dash：
#
# Performing tests
#
testAdding

#
# Test report
#
tests passed:     1  50%
tests failed:     0   0%
tests skipped:    1  50%
tests total:      2 100%

可以看到跳过了一个测试。
skipping的存在，方便了为不用运行环境编写通用测试脚本。

	回调函数
这一节介绍的几个函数在需要时可以自己编写，它们将在不同时候被shUnit2调用。
这里我们统称为回调函数。之前已经看到过的suite就是其中一个。

再来看4个会在测试前后被调用的函数：
函数原型
描述
oneTimeSetUp()

在所有测试开始前被调用
oneTimeTearDown()

在所有测试结束后被调用
setUp()

在每个测试开始前被调用
tearDown()
在每个测试结束后被调用

这组回调函数均需要用户自行编写，最常见的做法是在这些函数里修改一些环境变量，为测试做准备。
就像我们从add_test.sh里可以看到的那样，在oneTimeSetUp加载了add.sh脚本，把所有函数都导入到当前的环境中。
而tearDown函数可以用来恢复环境。

写个简单的小例子，能对这几个函数的调用顺序有更深的印象：
#!/bin/bash

oneTimeSetUp()
{
        echo 'hey, I am oneTimeSetUp'
}

oneTimeTearDown()
{
        echo 'hey, I am oneTimeTearDown'
}

setUp()
{
        echo 'do setUp'
}

tearDown()
{
        echo 'do tearDown'
}

test_example()
{
        :
}

test_example2()
{
        :
}

# load and run shUnit2
. ./shunit2

运行得到：
hey, I am oneTimeSetUp
#
# Performing tests
#
do setUp
test_example
do tearDown
do setUp
test_example2
do tearDown
hey, I am oneTimeTearDown

#
# Test report
#
tests passed:     0   0%
tests failed:     0   0%
tests skipped:    0   0%
tests total:      0   0%

好了，到这里基本已经把shUnit2框架的用法和功能展示完了。
文中的例子都是最简单最基本的，工作中的实际使用可能要复杂许多，这里推荐log4sh的单元测试脚本，
大家有时间可以通过阅读该脚本了解到该shell脚本单元测试框架如何被实际应用。


## shUnit2 Code

# $Id: shunit2 151 2008-05-10 22:07:29Z sfsetse $
# vim:et:ft=sh:sts=2:sw=2
# vim:foldmethod=marker:foldmarker=/**,*/
#
#/**
# <?xml version="1.0" encoding="UTF-8"?>
# <s:shelldoc xmlns:s="http://www.forestent.com/projects/shelldoc/xsl/2005.0">
# <s:header>
# shUnit 2.1.3
# Shell Unit Test Framework
#
# http://shunit2.sourceforge.net/
#
# written by Kate Ward &lt;kate.ward@forestent.com&gt;
# released under the LGPL
#
# this module implements a xUnit based unit test framework similar to JUnit
# </s:header>
#*/

__SHUNIT_VERSION='2.1.3'

_shunit_warn() { echo "shunit2:WARN $@" >&2; }
_shunit_error() { echo "shunit2:ERROR $@" >&2; }
_shunit_fatal() { echo "shunit2:FATAL $@" >&2; }

SHUNIT_TRUE=0
SHUNIT_FALSE=1
SHUNIT_ERROR=2

# specific shell checks
if [ -n "${ZSH_VERSION:-}" ]; then
  if [ "`set -o |grep "^shwordsplit" |awk '{print $2}'`" = 'off' ]; then
    _shunit_fatal 'shwordsplit option required for proper zsh operation'
    exit ${SHUNIT_ERROR}
  fi
  if [ -z "${SHUNIT_PARENT:-}" ]; then
    _shunit_fatal "zsh does not pass \$0 through properly. please declare \
\"SHUNIT_PARENT=\$0\" before calling shUnit2"
    exit ${SHUNIT_ERROR}
  fi
fi

# shell flags for shunit2:
# u - treat unset variables as an error when performing parameter expansion
__SHUNIT_SHELL_FLAGS='u'

# save the current set of shell flags, and then set some for ourself
_shunit_shellFlags="$-"
for _shunit_shellFlag in `echo "${__SHUNIT_SHELL_FLAGS}" |sed 's/\(.\)/\1 /g'`
do
  set -${_shunit_shellFlag}
done

#
# constants
#

__SHUNIT_ASSERT_MSG_PREFIX='ASSERT:'
__SHUNIT_PARENT=${SHUNIT_PARENT:-$0}

# set the constants readonly
_shunit_constants=`set |grep "^__SHUNIT_" |cut -d= -f1`
echo "${_shunit_constants}" |grep "^Binary file" >/dev/null
if [ $? -eq 0 ]; then
  # deal with binary junk in 'set' output
  _shunit_constants=`set |grep -a "^__SHUNIT_" |cut -d= -f1`
fi
for _shunit_const in ${_shunit_constants}; do
  if [ -n "${ZSH_VERSION:-}" ]; then
    readonly -g ${_shunit_const}  # declare readonly constants globally
  else
    readonly ${_shunit_const}
  fi
done
unset _shunit_const _shunit_constants

# variables
__shunit_skip=${SHUNIT_FALSE}
__shunit_suite=''

__shunit_testsPassed=0
__shunit_testsFailed=0
__shunit_testsSkipped=0
__shunit_testsTotal=0

#-----------------------------------------------------------------------------
# assert functions
#

#/**
# <s:function group="asserts">
# <entry align="right">
#   <emphasis>void</emphasis>
# </entry>
# <entry>
#   <funcsynopsis>
#     <funcprototype>
#       <funcdef><function>assertEquals</function></funcdef>
#       <paramdef>string <parameter>[message]</parameter></paramdef>
#       <paramdef>string <parameter>expected</parameter></paramdef>
#       <paramdef>string <parameter>actual</parameter></paramdef>
#     </funcprototype>
#   </funcsynopsis>
#   <para>Asserts that <emphasis>expected</emphasis> and
#   <emphasis>actual</emphasis> are equal to one another. The message is
#   optional.</para>
# </entry>
# </s:function>
#*/
assertEquals()
{
  _shunit_shouldSkip && return ${SHUNIT_TRUE}

  _su_message=''
  if [ $# -lt 2 ]; then
    _shunit_error 'assertEquals()/assertSame() require at least two arguments'
    return ${SHUNIT_ERROR}
  elif [ $# -eq 3 ]; then
    _su_message=$1
    shift
  fi
  _su_expected=${1:-}
  _su_actual=${2:-}

  shunit_return=${SHUNIT_TRUE}
  if [ "${_su_expected}" = "${_su_actual}" ]; then
    _shunit_testPassed
  else
    failNotEquals "${_su_message}" "${_su_expected}" "${_su_actual}"
    shunit_return=${SHUNIT_FALSE}
  fi

  unset _su_message _su_expected _su_actual
  return ${shunit_return}
}

#/**
# <s:function group="asserts">
# <entry align="right">
#   <emphasis>void</emphasis>
# </entry>
# <entry>
#   <funcsynopsis>
#     <funcprototype>
#       <funcdef><function>assertNull</function></funcdef>
#       <paramdef>string <parameter>[message]</parameter></paramdef>
#       <paramdef>string <parameter>value</parameter></paramdef>
#     </funcprototype>
#   </funcsynopsis>
#   <para>Asserts that <emphasis>value</emphasis> is <literal>null</literal>,
#   or in shell terms a zero-length string. The message is optional.</para>
# </entry>
# </s:function>
#*/
assertNull()
{
  _shunit_shouldSkip && return ${SHUNIT_TRUE}

  if [ $# -lt 1 ]; then
    _shunit_error 'assertNull() requires at least one arguments'
    return ${SHUNIT_ERROR}
  elif [ $# -eq 2 ]; then
    assertTrue "$1" "[ -z '$2' ]"
  else
    assertTrue "[ -z '${1:-}' ]"
  fi
}

#/**
# <s:function group="asserts">
# <entry align="right">
#   <emphasis>void</emphasis>
# </entry>
# <entry>
#   <funcsynopsis>
#     <funcprototype>
#       <funcdef><function>assertNotNull</function></funcdef>
#       <paramdef>string <parameter>[message]</parameter></paramdef>
#       <paramdef>string <parameter>value</parameter></paramdef>
#     </funcprototype>
#   </funcsynopsis>
#   <para>Asserts that <emphasis>value</emphasis> is <emphasis
#   role="strong">not</emphasis> <literal>null</literal>, or in shell terms not
#   a zero-length string. The message is optional.</para>
# </entry>
# </s:function>
#*/
assertNotNull()
{
  _shunit_shouldSkip && return ${SHUNIT_TRUE}

  if [ $# -eq 2 ]; then
    assertTrue "$1" "[ -n '$2' ]"
  else
    assertTrue "[ -n '${1:-}' ]"
  fi
}

#/**
# <s:function group="asserts">
# <entry align="right">
#   <emphasis>void</emphasis>
# </entry>
# <entry>
#   <funcsynopsis>
#     <funcprototype>
#       <funcdef><function>assertSame</function></funcdef>
#       <paramdef>string <parameter>[message]</parameter></paramdef>
#       <paramdef>string <parameter>expected</parameter></paramdef>
#       <paramdef>string <parameter>actual</parameter></paramdef>
#     </funcprototype>
#   </funcsynopsis>
#   <para>This function is functionally equivalent to
#   <function>assertEquals</function>.</para>
# </entry>
# </s:function>
#*/
assertSame()
{
  assertEquals "${@:-}"
}

#/**
# <s:function group="asserts">
# <entry align="right">
#   <emphasis>void</emphasis>
# </entry>
# <entry>
#   <funcsynopsis>
#     <funcprototype>
#       <funcdef><function>assertNotSame</function></funcdef>
#       <paramdef>string <parameter>[message]</parameter></paramdef>
#       <paramdef>string <parameter>unexpected</parameter></paramdef>
#       <paramdef>string <parameter>actual</parameter></paramdef>
#     </funcprototype>
#   </funcsynopsis>
#   <para>Asserts that <emphasis>unexpected</emphasis> and
#   <emphasis>actual</emphasis> are <emphasis role="strong">not</emphasis>
#   equal to one another. The message is optional.</para>
# </entry>
# </s:function>
#*/
assertNotSame()
{
  _shunit_shouldSkip && return ${SHUNIT_TRUE}

  _su_message=''
  if [ $# -eq 3 ]; then
    _su_message=$1
    shift
  fi
  _su_unexpected=${1:-}
  _su_actual=${2:-}

  shunit_return=${SHUNIT_TRUE}
  if [ "${_su_unexpected}" != "${_su_actual}" ]; then
    _shunit_testPassed
  else
    failSame "${_su_message}"
    shunit_return=${SHUNIT_FALSE}
  fi

  unset _su_message _su_unexpected _su_actual
  return ${shunit_return}
}

#/**
# <s:function group="asserts">
# <entry align="right">
#   <emphasis>void</emphasis>
# </entry>
# <entry>
#   <funcsynopsis>
#     <funcprototype>
#       <funcdef><function>assertTrue</function></funcdef>
#       <paramdef>string <parameter>[message]</parameter></paramdef>
#       <paramdef>string <parameter>condition</parameter></paramdef>
#     </funcprototype>
#   </funcsynopsis>
#   <para>Asserts that a given shell test condition is true. The message is
#   optional.</para>
#   <para>Testing whether something is true or false is easy enough by using
#   the assertEquals/assertNotSame functions. Shell supports much more
#   complicated tests though, and a means to support them was needed. As such,
#   this function tests that conditions are true or false through evaluation
#   rather than just looking for a true or false.</para>
#   <funcsynopsis>
#     The following test will succeed: <funcsynopsisinfo>assertTrue "[ 34 -gt 23 ]"</funcsynopsisinfo>
#     The folloing test will fail with a message: <funcsynopsisinfo>assertTrue "test failed" "[ -r '/non/existant/file' ]"</funcsynopsisinfo>
#   </funcsynopsis>
# </entry>
# </s:function>
#*/
assertTrue()
{
  _shunit_shouldSkip && return ${SHUNIT_TRUE}

  _su_message=''
  if [ $# -eq 2 ]; then
    _su_message=$1
    shift
  fi
  _su_condition=${1:-}

  shunit_return=${SHUNIT_TRUE}

  # see if condition is an integer, i.e. a return value
  _su_match=`expr "${_su_condition}" : '\([0-9]*\)'`
  if [ -z "${_su_condition}" ]; then
    # null condition
    shunit_return=${SHUNIT_FALSE}
  elif [ "${_su_condition}" = "${_su_match}" ]; then
    # possible return value. treating 0 as true, and non-zero as false.
    [ ${_su_condition} -ne 0 ] && shunit_return=${SHUNIT_FALSE}
  else
    # (hopefully) a condition
    ( eval ${_su_condition} ) >/dev/null 2>&1
    [ $? -ne 0 ] && shunit_return=${SHUNIT_FALSE}
  fi

  # record the test
  if [ ${shunit_return} -eq ${SHUNIT_TRUE} ]; then
    _shunit_testPassed
  else
    _shunit_testFailed "${_su_message}"
  fi

  unset _su_message _su_condition _su_match
  return ${shunit_return}
}

#/**
# <s:function group="asserts">
# <entry align="right">
#   <emphasis>void</emphasis>
# </entry>
# <entry>
#   <funcsynopsis>
#     <funcprototype>
#       <funcdef><function>assertFalse</function></funcdef>
#       <paramdef>string <parameter>[message]</parameter></paramdef>
#       <paramdef>string <parameter>condition</parameter></paramdef>
#     </funcprototype>
#   </funcsynopsis>
#   <para>Asserts that a given shell test condition is false. The message is
#   optional.</para>
#   <para>Testing whether something is true or false is easy enough by using
#   the assertEquals/assertNotSame functions. Shell supports much more
#   complicated tests though, and a means to support them was needed. As such,
#   this function tests that conditions are true or false through evaluation
#   rather than just looking for a true or false.</para>
#   <funcsynopsis>
#     The following test will succeed: <funcsynopsisinfo>assertFalse "[ 'apples' = 'oranges' ]"</funcsynopsisinfo>
#     The folloing test will fail with a message: <funcsynopsisinfo>assertFalse "test failed" "[ 1 -eq 1 -a 2 -eq 2 ]"</funcsynopsisinfo>
#   </funcsynopsis>
# </entry>
# </s:function>
#*/
assertFalse()
{
  _shunit_shouldSkip && return ${SHUNIT_TRUE}

  _su_message=''
  if [ $# -eq 2 ]; then
    _su_message=$1
    shift
  fi
  _su_condition=${1:-}

  shunit_return=${SHUNIT_TRUE}

  # see if condition is an integer, i.e. a return value
  _su_match=`expr "${_su_condition}" : '\([0-9]*\)'`
  if [ -z "${_su_condition}" ]; then
    # null condition
    shunit_return=${SHUNIT_FALSE}
  elif [ "${_su_condition}" = "${_su_match}" ]; then
    # possible return value. treating 0 as true, and non-zero as false.
    [ ${_su_condition} -eq 0 ] && shunit_return=${SHUNIT_FALSE}
  else
    # (hopefully) a condition
    ( eval ${_su_condition} ) >/dev/null 2>&1
    [ $? -eq 0 ] && shunit_return=${SHUNIT_FALSE}
  fi

  # record the test
  if [ ${shunit_return} -eq ${SHUNIT_TRUE} ]; then
    _shunit_testPassed
  else
    _shunit_testFailed "${_su_message}"
  fi

  unset _su_message _su_condition _su_match
  return ${shunit_return}
}

#-----------------------------------------------------------------------------
# failure functions
#

#/**
# <s:function group="failures">
# <entry align="right">
#   <emphasis>void</emphasis>
# </entry>
# <entry>
#   <funcsynopsis>
#     <funcprototype>
#       <funcdef><function>fail</function></funcdef>
#       <paramdef>string <parameter>[message]</parameter></paramdef>
#     </funcprototype>
#   </funcsynopsis>
#   <para>Fails the test immediately, with the optional message.</para>
# </entry>
# </s:function>
#*/
fail()
{
  _shunit_shouldSkip && return ${SHUNIT_TRUE}

  _su_message=${1:-}

  _shunit_testFailed "${_su_message}"

  unset _su_message
}

#/**
# <s:function group="failures">
# <entry align="right">
#   <emphasis>void</emphasis>
# </entry>
# <entry>
#   <funcsynopsis>
#     <funcprototype>
#       <funcdef><function>failNotEquals</function></funcdef>
#       <paramdef>string <parameter>[message]</parameter></paramdef>
#       <paramdef>string <parameter>unexpected</parameter></paramdef>
#       <paramdef>string <parameter>actual</parameter></paramdef>
#     </funcprototype>
#   </funcsynopsis>
#   <para>Fails the test if <emphasis>unexpected</emphasis> and
#   <emphasis>actual</emphasis> are <emphasis role="strong">not</emphasis>
#   equal to one another. The message is optional.</para>
# </entry>
# </s:function>
#*/
failNotEquals()
{
  _shunit_shouldSkip && return ${SHUNIT_TRUE}

  _su_message=''
  if [ $# -eq 3 ]; then
    _su_message=$1
    shift
  fi
  _su_unexpected=${1:-}
  _su_actual=${2:-}

  _shunit_testFailed "${_su_message:+${_su_message} }expected:<${_su_unexpected}> but was:<${_su_actual}>"

  unset _su_message _su_unexpected _su_actual
}

#/**
# <s:function group="failures">
# <entry align="right">
#   <emphasis>void</emphasis>
# </entry>
# <entry>
#   <funcsynopsis>
#     <funcprototype>
#       <funcdef><function>failSame</function></funcdef>
#       <paramdef>string <parameter>[message]</parameter></paramdef>
#     </funcprototype>
#   </funcsynopsis>
#   <para>Indicate test failure because arguments were not the same. The
#   message is optional.</para>
# </entry>
# </s:function>
#*/
failSame()
{
  _shunit_shouldSkip && return ${SHUNIT_TRUE}

  _su_message=${1:-}

  _shunit_testFailed "${_su_message:+${_su_message} }expected not same"

  unset _su_message
}

#/**
# <s:function group="failures">
# <entry align="right">
#   <emphasis>void</emphasis>
# </entry>
# <entry>
#   <funcsynopsis>
#     <funcprototype>
#       <funcdef><function>failNotSame</function></funcdef>
#       <paramdef>string <parameter>[message]</parameter></paramdef>
#       <paramdef>string <parameter>expected</parameter></paramdef>
#       <paramdef>string <parameter>actual</parameter></paramdef>
#     </funcprototype>
#   </funcsynopsis>
#   <para>Fails the test if <emphasis>expected</emphasis> and
#   <emphasis>actual</emphasis> are equal to one another. The message is
#   optional.</para>
# </entry>
# </s:function>
#*/
failNotSame()
{
  failNotEquals "${@:-}"
}

#-----------------------------------------------------------------------------
# skipping functions
#

#/**
# <s:function group="skipping">
# <entry align="right">
#   <emphasis>void</emphasis>
# </entry>
# <entry>
#   <funcsynopsis>
#     <funcprototype>
#       <funcdef><function>startSkipping</function></funcdef>
#       <paramdef />
#     </funcprototype>
#   </funcsynopsis>
#   <para>This function forces the remaining assert and fail functions to be
#   "skipped", i.e. they will have no effect. Each function skipped will be
#   recorded so that the total of asserts and fails will not be altered.</para>
# </entry>
# </s:function>
#*/
startSkipping()
{
  __shunit_skip=${SHUNIT_TRUE}
}

#/**
# <s:function group="skipping">
# <entry align="right">
#   <emphasis>void</emphasis>
# </entry>
# <entry>
#   <funcsynopsis>
#     <funcprototype>
#       <funcdef><function>endSkipping</function></funcdef>
#       <paramdef />
#     </funcprototype>
#   </funcsynopsis>
#   <para>This function returns calls to the assert and fail functions to their
#   default behavior, i.e. they will be called.</para>
# </entry>
# </s:function>
#*/
endSkipping()
{
  __shunit_skip=${SHUNIT_FALSE}
}

#/**
# <s:function group="skipping">
# <entry align="right">
#   <emphasis>boolean</emphasis>
# </entry>
# <entry>
#   <funcsynopsis>
#     <funcprototype>
#       <funcdef><function>isSkipping</function></funcdef>
#       <paramdef />
#     </funcprototype>
#   </funcsynopsis>
#   <para>This function returns the state of skipping.</para>
# </entry>
# </s:function>
#*/
isSkipping()
{
  return ${__shunit_skip}
}

#-----------------------------------------------------------------------------
# suite functions
#

#/**
# <s:function group="suites">
# <entry align="right">
#   <emphasis>void</emphasis>
# </entry>
# <entry>
#   <funcsynopsis>
#     <funcprototype>
#       <funcdef><function>suite</function></funcdef>
#       <paramdef />
#     </funcprototype>
#   </funcsynopsis>
#   <para>This function can be optionally overridden by the user in their test
#   suite.</para>
#   <para>If this function exists, it will be called when
#   <command>shunit2</command> is sourced. If it does not exist, shUnit2 will
#   search the parent script for all functions beginning with the word
#   <literal>test</literal>, and they will be added dynamically to the test
#   suite.</para>
# </entry>
# </s:function>
#*/
# Note: see _shunit_mktempFunc() for actual implementation
# suite() { :; }

#/**
# <s:function group="suites">
# <entry align="right">
#   <emphasis>void</emphasis>
# </entry>
# <entry>
#   <funcsynopsis>
#     <funcprototype>
#       <funcdef><function>suite_addTest</function></funcdef>
#       <paramdef>string <parameter>function</parameter></paramdef>
#     </funcprototype>
#   </funcsynopsis>
#   <para>This function adds a function name to the list of tests scheduled for
#   execution as part of this test suite. This function should only be called
#   from within the <function>suite()</function> function.</para>
# </entry>
# </s:function>
#*/
suite_addTest()
{
  _su_func=${1:-}

  __shunit_suite="${__shunit_suite:+${__shunit_suite} }${_su_func}"

  unset _su_func
}

#/**
# <s:function group="suites">
# <entry align="right">
#   <emphasis>void</emphasis>
# </entry>
# <entry>
#   <funcsynopsis>
#     <funcprototype>
#       <funcdef><function>oneTimeSetUp</function></funcdef>
#       <paramdef />
#     </funcprototype>
#   </funcsynopsis>
#   <para>This function can be be optionally overridden by the user in their
#   test suite.</para>
#   <para>If this function exists, it will be called once before any tests are
#   run. It is useful to prepare a common environment for all tests.</para>
# </entry>
# </s:function>
#*/
# Note: see _shunit_mktempFunc() for actual implementation
# oneTimeSetUp() { :; }

#/**
# <s:function group="suites">
# <entry align="right">
#   <emphasis>void</emphasis>
# </entry>
# <entry>
#   <funcsynopsis>
#     <funcprototype>
#       <funcdef><function>oneTimeTearDown</function></funcdef>
#       <paramdef />
#     </funcprototype>
#   </funcsynopsis>
#   <para>This function can be be optionally overridden by the user in their
#   test suite.</para>
#   <para>If this function exists, it will be called once after all tests are
#   completed. It is useful to clean up the environment after all tests.</para>
# </entry>
# </s:function>
#*/
# Note: see _shunit_mktempFunc() for actual implementation
# oneTimeTearDown() { :; }

#/**
# <s:function group="suites">
# <entry align="right">
#   <emphasis>void</emphasis>
# </entry>
# <entry>
#   <funcsynopsis>
#     <funcprototype>
#       <funcdef><function>setUp</function></funcdef>
#       <paramdef />
#     </funcprototype>
#   </funcsynopsis>
#   <para>This function can be be optionally overridden by the user in their
#   test suite.</para>
#   <para>If this function exists, it will be called before each test is run.
#   It is useful to reset the environment before each test.</para>
# </entry>
# </s:function>
#*/
# Note: see _shunit_mktempFunc() for actual implementation
# setUp() { :; }

#/**
# <s:function group="suites">
# <entry align="right">
#   <emphasis>void</emphasis>
# </entry>
# <entry>
#   <funcsynopsis>
#     <funcprototype>
#       <funcdef><function>tearDown</function></funcdef>
#       <paramdef />
#     </funcprototype>
#   </funcsynopsis>
#   <para>This function can be be optionally overridden by the user in their
#   test suite.</para>
#   <para>If this function exists, it will be called after each test completes.
#   It is useful to clean up the environment after each test.</para>
# </entry>
# </s:function>
#*/
# Note: see _shunit_mktempFunc() for actual implementation
# tearDown() { :; }

#------------------------------------------------------------------------------
# internal shUnit2 functions
#

_shunit_cleanup()
{
  name=$1

  case ${name} in
    EXIT) signal=0 ;;
    INT) signal=2 ;;
    TERM) signal=15 ;;
    *)
      _shunit_warn "unrecognized trap value (${name})"
      signal=0
      ;;
  esac

  # do our work
  rm -fr "${__shunit_tmpDir}"

  # exit for all non-EXIT signals
  if [ ${name} != 'EXIT' ]; then
    _shunit_warn "trapped and now handling the (${name}) signal"
    _shunit_generateReport
    # disable EXIT trap
    trap 0
    # add 127 to signal and exit
    signal=`expr ${signal} + 127`
    exit ${signal}
  fi
}

_shunit_execSuite()
{
  echo '#'
  echo '# Performing tests'
  echo '#'
  for _su_func in ${__shunit_suite}; do
    # disable skipping
    endSkipping

    # execute the per-test setup function
    setUp

    # execute the test
    echo "${_su_func}"
    eval ${_su_func}

    # execute the per-test tear-down function
    tearDown
  done

  unset _su_func
}

_shunit_functionExists()
{
  _su__func=$1
  type ${_su__func} 2>/dev/null |grep "is a function$" >/dev/null
  _su__return=$?
  unset _su__func
  return ${_su__return}
}

_shunit_generateReport()
{
  _su__awkPercent='{printf("%4d %3.0f%%", $1, $1*100/$2)}'
  if [ ${__shunit_testsTotal:-0} -gt 0 ]; then
    _su__passed=`echo ${__shunit_testsPassed} ${__shunit_testsTotal} |\
        awk "${_su__awkPercent}"`
    _su__failed=`echo ${__shunit_testsFailed} ${__shunit_testsTotal} |\
        awk "${_su__awkPercent}"`
    _su__skipped=`echo ${__shunit_testsSkipped} ${__shunit_testsTotal} |\
        awk "${_su__awkPercent}"`
    _su__total=`echo ${__shunit_testsTotal} 100 |\
        awk '{printf("%4d %3d%%", $1, $2)}'`
  else
    _su__passed=`echo 0 0 |awk '{printf("%4d %3d%%", $1, $2)}'`
    _su__failed=${_su__passed}
    _su__skipped=${_su__passed}
    _su__total=${_su__passed}
  fi

  cat <<EOF

#
# Test report
#
tests passed:  ${_su__passed}
tests failed:  ${_su__failed}
tests skipped: ${_su__skipped}
tests total:   ${_su__total}
EOF

  unset _su__awkPercent _su__passed _su__failed _su__skipped _su__total
}

# this function is a cross-platform temporary directory creation tool. not all
# OSes have the mktemp function, so one is included here.
_shunit_mktempDir()
{
  # try the standard mktemp function
  ( exec mktemp -dqt shunit.XXXXXX 2>/dev/null ) && return

  # the standard mktemp didn't work.  doing our own.
  if [ -r '/dev/urandom' ]; then
    _su__random=`od -vAn -N4 -tx4 </dev/urandom |sed 's/^[^0-9a-f]*//'`
  elif [ -n "${RANDOM:-}" ]; then
    # $RANDOM works
    _su__random=${RANDOM}${RANDOM}${RANDOM}$$
  else
    # $RANDOM doesn't work
    _su__date=`date '+%Y%m%d%H%M%S'`
    _su__random=`expr ${_su__date} / $$`
  fi

  _su__tmpDir="${TMPDIR-/tmp}/shunit.${_su__random}"
  ( umask 077 && mkdir "${_su__tmpDir}" ) || {
    echo 'shUnit:FATAL could not create temporary directory! exiting' >&2
    exit 1
  }

  echo ${_su__tmpDir}
  unset _su__date _su__random _su__tmpDir
}

# this function is here to work around issues in Cygwin
_shunit_mktempFunc()
{
  for _su__func in oneTimeSetUp oneTimeTearDown setUp tearDown suite; do
    _su__file="${__shunit_tmpDir}/${_su__func}"
    cat <<EOF >"${_su__file}"
#! /bin/sh
exit 0
EOF
    chmod +x "${_su__file}"
  done

  unset _su__file
}

_shunit_shouldSkip()
{
  [ ${__shunit_skip} -eq ${SHUNIT_FALSE} ] && return ${SHUNIT_FALSE}
  _shunit_testSkipped
}

_shunit_testPassed()
{
  __shunit_testsPassed=`expr ${__shunit_testsPassed} + 1`
  __shunit_testsTotal=`expr ${__shunit_testsTotal} + 1`
}

_shunit_testFailed()
{
  _su__msg=$1

  __shunit_testsFailed=`expr ${__shunit_testsFailed} + 1`
  __shunit_testsTotal=`expr ${__shunit_testsTotal} + 1`
  echo "${__SHUNIT_ASSERT_MSG_PREFIX}${_su__msg}" >&2

  unset _su__msg
}

_shunit_testSkipped()
{
  __shunit_testsSkipped=`expr ${__shunit_testsSkipped} + 1`
  __shunit_testsTotal=`expr ${__shunit_testsTotal} + 1`
}

#------------------------------------------------------------------------------
# main
#

# create a temporary storage location
__shunit_tmpDir=`_shunit_mktempDir`

# setup traps to clean up after ourselves
trap '_shunit_cleanup EXIT' 0
trap '_shunit_cleanup INT' 2
trap '_shunit_cleanup TERM' 15

# create phantom functions to work around issues with Cygwin
_shunit_mktempFunc
PATH="${__shunit_tmpDir}:${PATH}"

# execute the oneTimeSetUp function (if it exists)
#_shunit_functionExists oneTimeSetUp && oneTimeSetUp
oneTimeSetUp

# execute the suite function defined in the parent test script
# deprecated as of 2.1.0
suite

# if no suite function was defined, dynamically build a list of functions
if [ -z "${__shunit_suite}" ]; then
  funcs=`grep "^[ \t]*test[A-Za-z0-9_]* *()" ${__SHUNIT_PARENT} \
      |sed 's/[^A-Za-z0-9_]//g'`
  for func in ${funcs}; do
    suite_addTest ${func}
  done
fi

# execute the tests
_shunit_execSuite

# execute the oneTimeTearDown function (if it exists)
oneTimeTearDown

# generate report
_shunit_generateReport

# restore the previous set of shell flags
for _shunit_shellFlag in ${__SHUNIT_SHELL_FLAGS}; do
  echo ${_shunit_shellFlags} |grep ${_shunit_shellFlag} >/dev/null \
    || set +${_shunit_shellFlag}
done
unset _shunit_shellFlag _shunit_shellFlags

[ ${__shunit_testsFailed} -eq 0 ] || exit 1

#/**
# </s:shelldoc>
#*/

## shunit2_example

### readme.txt

string.sh                              # string.sh 里是一个字符串处理的函数库

test_string.sh                      # string.sh的测试脚本

shunit2                               # shunit2测试框架 
    
shunit2主要的应用场合还是这类函数库（模块化的脚本），每个函数都有比较明确的输入输出或者返回值。
对于从头写到尾没有分函数的脚本为了测试修改成本就比较高，对于函数内部大量调用外部命令的这种情况就需要添加
对这些外部命令返回值的判断才能更好的完成测试。

string.sh是一个比较适合进行单元测试的例子，大家可以看看。

### string.sh

#!/bin/bash
##! @TODO:	string library
##! @VERSION:	0.1beta
##! @AUTHOR:	wengjy
##! @DATE:	2008-07-25
##! @LASTMODIFY:2008-07-25
##! @DEP:	expr 
##! @DEP:	rev

################### Global Variable ###################
readonly DEBUG_STR_LIB=0

###################### Function #######################
##! @TODO: 把字符串转换成小写 
##! @AUTHOR: wengjy
##! @VERSION: 1.0
##! @IN: $@ => src
##! @OUT: 转换后的字符串
##! @RET: 0 => success
function tolower()
{
	echo $@|tr [A-Z] [a-z]
	return 0
}

##! @TODO: 把字符串转换成大写
##! @AUTHOR: wengjy
##! @VERSION: 1.0
##! @IN: $@ => src
##! @OUT: 转换后的字符串
##! @RET: 0 => success
function toupper()
{
	echo $@|tr [a-z] [A-Z]
	return 0
}

##! @TODO: 连接字符串
##! @AUTHOR: wengjy
##! @VERSION: 1.0
##! @IN: $@ => src
##! @OUT: 连接后的字符串
##! @RET: 0 => success
function strcat()
{
	local ret=""

	for i in $@
	do
		ret=${ret}${i}
	done

	echo $ret
	return 0
}

##! @TODO: 获取字符串长度
##! @AUTHOR: wengjy
##! @VERSION: 1.0
##! @IN: $1 => src
##! @OUT: 字符串长度
##! @RET: 0 => success
function strlen()
{
	echo ${#1}
	return 0
}

##! @TODO: 比较字符串
##! @AUTHOR: wengjy
##! @VERSION: 1.0
##! @IN: $1 => str1; $2 => str2
##! @RET: 0 => 两字符串一致; 1 => 字符串不一致;
function strcmp()
{
	[[ $# != 2 ]] && return 1
	[[ "$1" == "$2" ]] && return 0 || return 1
}

##! @TODO: 比较字符串(不区分大小写)
##! @AUTHOR: wengjy
##! @VERSION: 1.0
##! @IN: $1 => str1; $2 => str2
##! @RET: 0 => 两字符串一致; 1 => 字符串不一致;
function stricmp()
{
	[[ $# != 2 ]] && return 1

	local str1=`tolower $1`
	local str2=`tolower $2`
	[[ "$str1" == "$str2" ]] && return 0 || return 1
}

##! @TODO: 从字符串里截取子串
##! @AUTHOR: wengjy
##! @VERSION: 1.0
##! @IN: $1 => src; $2 => start pos; $3 => len
##! @OUT: 子串
##! @RET: 0 => success; 1 => failure
function substr()
{
	local src=$1
	local start_pos=$2

	if [[ $# == 3 ]]
	then
		local len=$3
		echo ${src:start_pos:len}
	elif [[ $# == 2 ]]
	then
		echo ${src:start_pos}
	else
		return 1
	fi
	return 0
}

##! @TODO: 从字符串里查找子串
##! @AUTHOR: wengjy
##! @VERSION: 1.0
##! @IN: $1 => src; $2 => 目标子串
##! @OUT: 子串所在偏移（从1开始，当偏移为0时即找不到该子串）
##! @RET: 0 => success
function index()
{
	[[ $# != 2 ]] && { echo 0; return 0; }

	expr index $1 $2
	return 0
}

##! @TODO: 反转字符串
##! @AUTHOR: wengjy
##! @VERSION: 1.0
##! @IN: $@ => src
##! @OUT: 反转后字符串
##! @RET: 0 => success
function strrev()
{
	echo $@|rev
	return 0
}

# Main
######################### Example ########################
if [ $DEBUG_STR_LIB -eq 1 ] 
then

tolower A a B b ABCD abcd
toupper A a B b ABCD abcd

strcat a b c d
strlen abcd
strcmp abc abc
echo $?
strcmp abc ABC
echo $?

stricmp abc ABC
echo $?

substr abcd 1 
substr abcd 1 2 

index abcd bc 
index abcd c

strrev abcd efg hi

fi


### test_string.sh

#!/bin/bash

testTolower()
{
	out=`tolower A a B b ABCD abcd`
	assertEquals "the result of '${out}' was wrong" "${out}" "a a b b abcd abcd"
}

testToupper()
{
        out=`toupper A a B b ABCD abcd`
        assertEquals "the result of '${out}' was wrong" "${out}" "A A B B ABCD ABCD"
}

testStrcat()
{
	out=`strcat a b c d`
	assertEquals "the result of '${out}' was wrong" "${out}" "abcd"
}

testStrlen()
{
	out=`strlen abcd`
	assertEquals "the result of '${out}' was wrong" ${out} 4
}

testStrcmp()
{
	strcmp abc abc
	ret=$?
	assertEquals "the result of '${ret}' was wrong" ${ret} 0

	strcmp abc ABC
	ret=$?
	assertEquals "the result of '${ret}' was wrong" ${ret} 1
}

testStricmp()
{
	stricmp abc ABC
	ret=$?
	assertEquals "the result of '${ret}' was wrong" ${ret} 0
}

testSubstr()
{
	out=`substr abcd 1`
	assertEquals "the result of '${out}' was wrong" "${out}" "bcd"

	out=`substr abcd 1 2`
	assertEquals "the result of '${out}' was wrong" "${out}" "bc"
}

testIndex()
{
	out=`index abcd bc`
	assertEquals "the result of '${out}' was wrong" "${out}" 2

	out=`index abcd c`
	assertEquals "the result of '${out}' was wrong" "${out}" 3

	out=`index abcd k`
	assertEquals "the result of '${out}' was wrong" "${out}" 0
}

testStrrev()
{
	out=`strrev abcd efg hi`
	assertEquals "the result of '${out}' was wrong" "${out}" "ih gfe dcba"
}

oneTimeSetUp()
{
	. ./string.sh
}

. ./shunit2

