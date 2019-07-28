# [Shell Knowledge System](KS-Shell/README.md)
Knowledge System of Shell.

# two-minutes-of-Shell
Curated collection of useful Shell knowledge points or snippets 
that can study in five minutes or less. 

## 目录
* [变量](#变量)
  * [:smile:$@与$*](#与)
* [Pitfall](#pitfall)
  * [:smile:包含空格的参数传递](#包含空格的参数传递)

## 变量
### :sunny: $@与$*
```Bash
#!/usr/bin/env bash
function func1() {
  echo '>> TEST $* OUTPUT: '
  func2 $*
  echo '>> TEST $@ OUTPUT: '
  func2 $@
  echo '>> TEST "$*" OUTPUT: '
  func2 "$*"
  echo '>> TEST "$@" OUTPUT: '
  func2 "$@"
}

function func2() {
  echo '$1 -> '"$1"
  echo '$2 -> '"$2"
  echo '$3 -> '"$3"
}

# Test
func1 "1.1 1.2" "2.1"
```
```md
$* $@ "$*" "$@" 作为参数传递的输出：
>> TEST $* OUTPUT:
$1 -> 1.1
$2 -> 1.2
$3 -> 2.1
>> TEST $@ OUTPUT:
$1 -> 1.1
$2 -> 1.2
$3 -> 2.1
>> TEST "$*" OUTPUT:
$1 -> 1.1 1.2 2.1
$2 ->
$3 ->
>> TEST "$@" OUTPUT:
$1 -> 1.1 1.2
$2 -> 2.1
$3 ->
```
```md
总结：
1. $* $@ 无区别，所有参数整体会按照 IFS指定分隔符(默认空格)分割，重新作为参数，
2. "$*" "$@" 加双引号的引用才有意义，"$*"把参数作为整体传递，"$@" 仍按照原有参数传递。
```

## Pitfall
### :sunny: 包含空格的参数传递
```Bash
function func1() {
  echo '$1 -> '$1
  echo '$2 -> '$2
}

# Test
var1="I'am Sunny!"

echo "TEST1: func1 \"I'am Sunny!\""
func1 "I'am Sunny!"

echo 'TEST2: func1 $var1'
func1 $var1

echo 'TEST3: func1 "$var1"'
func1 "$var1"
```
```md
输出：
TEST1: func1 "I'am Sunny!"
$1 -> I'am Sunny!
$2 ->
TEST2: func1 $var1
$1 -> I'am
$2 -> Sunny!
TEST3: func1 "$var1"
$1 -> I'am Sunny!
$2 ->
```
***变量作为参数传递时，变量的引用需要加双引号，以保证变量值作为整体传递***

--------------------------------
