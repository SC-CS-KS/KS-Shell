# five-minutes-of-Shell
```md
Curated collection of useful Shell knowledge points or snippets that can study in five minutes or less. 
```
## Basic
### 变量
#### 变量引用


### 函数


## Pitfall
* 包含空格的参数传递
```sh
#!/usr/bin/env bash
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
*** 变量作为参数传递时，变量的引用需要加双引号，以保证变量值作为整体传递 ***


## References
* [30-seconds-of-code](https://github.com/30-seconds/30-seconds-of-code) 
