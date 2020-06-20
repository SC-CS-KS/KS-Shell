# 12.字符串 函数

常见awk内置字符串函数 
index(in, find)
返回字符串in中字符串find第一次出现的位置(索引从1开始)，如果在字串in中找不到字符串find，则返回值为0。
eg: print index("peanut","an")会印出3
length(s)
求出字符串s的字符个数。
eg: length("abcde") 是5。
match(s,r)
返回模式字符串r在字符串s的第一次出现的位置，如果s不包含r，则返回值0。
sprintf(fmt,exp1,...)
和printf类似印出，是sprintf不是打印而是返回经fmt格式化后的exp。
eg: sprintf("pi = %.2f (approx.)",22/7) 传回的字串为"pi = 3.14 (approx.)" 
sub(p, r,t)
在字符串t中寻找符合模式字符串p的最靠前最长的位置，并以字符串r代替最前的p。 
eg: str = "water, water"sub(/at/, "ith",str) 结果字符串str 会变成"wither, water"
gsub(p, r, t)
gsub与sub类似。不过时在字符串t中以字符串r 代替所有的p。 
eg: str="water, water" ; gsub(/at/, "ith",str) 结果字符串str会变成"wither,wither"
substr(str, st, len) 
传回str的子字符串，其长度为len字符，从str的第st个位置开始。
如果len没有出现，则传回的子字符串是从第st个位置开始至结束。 
eg: substr("washington",5,3) 传回值为"ing", substr("washington",5) 传回值为"ington"
split(s,a,fs)
在分隔符fs为分隔符将字符串s分隔成一个awk数组a，并返回a的下标数。 
eg: awk 'BEGIN{print split("123#456#789",myarray,"#")}' 将打印 3 。 
tolower(str) 
将字符串str的大写字母改为小写字母。 
eg: tolower("MiXeD cAsE 123") 传回值为"mixed case 123" 
toupper(str)
将字符串string 的小写字母改为大写字母。 
eg: toupper("MiXeD cAsE 123")传回值为"MIXED CASE 123" 


函数
说明
gsub( Ere, Repl, [ In ] )
除了正则表达式所有具体值被替代这点，它和 sub 函数完全一样地执行，。
sub( Ere, Repl, [ In ] )
用 Repl 参数指定的字符串替换 In 参数指定的字符串中的由 Ere 参数指定的扩展正则表达式的第一个具体值。sub 函数返回替换的数量。出现在 Repl 参数指定的字符串中的 &（和符号）由 In 参数指定的与 Ere 参数的指定的扩展正则表达式匹配的字符串替换。如果未指定 In 参数，缺省值是整个记录（$0 记录变量）。
index( String1, String2 )
在由 String1 参数指定的字符串（其中有出现 String2 指定的参数）中，返回位置，从 1 开始编号。如果 String2 参数不在 String1 参数中出现，则返回 0（零）。
length [(String)]
返回 String 参数指定的字符串的长度（字符形式）。如果未给出 String 参数，则返回整个记录的长度（$0 记录变量）。
blength [(String)]
返回 String 参数指定的字符串的长度（以字节为单位）。如果未给出 String 参数，则返回整个记录的长度（$0 记录变量）。
substr( String, M, [ N ] )
返回具有 N 参数指定的字符数量子串。子串从 String 参数指定的字符串取得，其字符以 M 参数指定的位置开始。M 参数指定为将 String 参数中的第一个字符作为编号 1。如果未指定 N 参数，则子串的长度将是 M 参数指定的位置到 String 参数的末尾 的长度。
match( String, Ere )
在 String 参数指定的字符串（Ere 参数指定的扩展正则表达式出现在其中）中返回位置（字符形式），从 1 开始编号，或如果 Ere 参数不出现，则返回 0（零）。RSTART 特殊变量设置为返回值。RLENGTH 特殊变量设置为匹配的字符串的长度，或如果未找到任何匹配，则设置为 -1（负一）。
split( String, A, [Ere] )
将 String 参数指定的参数分割为数组元素 A[1], A[2], . . ., A[n]，并返回 n 变量的值。此分隔可以通过 Ere 参数指定的扩展正则表达式进行，或用当前字段分隔符（FS 特殊变量）来进行（如果没有给出 Ere 参数）。除非上下文指明特定的元素还应具有一个数字值，否则 A 数组中的元素用字符串值来创建。
tolower( String )
返回 String 参数指定的字符串，字符串中每个大写字符将更改为小写。大写和小写的映射由当前语言环境的 LC_CTYPE 范畴定义。
toupper( String )
返回 String 参数指定的字符串，字符串中每个小写字符将更改为大写。大写和小写的映射由当前语言环境的 LC_CTYPE 范畴定义。
sprintf(Format, Expr, Expr, . . . )
根据 Format 参数指定的 printf 子例程格式字符串来格式化 Expr 参数指定的表达式并返回最后生成的字符串。
