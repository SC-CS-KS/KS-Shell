# Pitfall

## :sunny: 包含空格的参数传递
```bash
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
输出：
```md
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
