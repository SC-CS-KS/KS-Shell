# 01.Shell Bash Expansion

       Expansion is performed on the command line after it has been split into words.  There are seven kinds of expansion performed: brace  expan-
       sion,  tilde  expansion,  parameter and variable expansion, command substitution, arithmetic expansion, word splitting, and pathname expan-
       sion.

       The order of expansions is: brace expansion, tilde expansion, parameter, variable and arithmetic expansion and command  substitution  (done
       in a left-to-right fashion), word splitting, and pathname expansion.

       On systems that can support it, there is an additional expansion available: process substitution.

       Only  brace  expansion,  word  splitting, and pathname expansion can change the number of words of the expansion; other expansions expand a single word to a single word.  The only exceptions to this are the expansions of "$@" and "${name[@]}" as explained above (see PARAMETERS).

扩展(EXPANSION)  
命令行的扩展是在拆分成词之后进行的。
有七种类型的扩展：  
brace expansion(花括号扩展),   
tilde expansion(波浪线扩展), 
parameter and variable expansion(参数和变量扩展), 
command substitution(命令替换), 
arithmetic expansion(算术扩展), 
word splitting(词的拆分), 和 pathname expansion(路径扩展).  
 
扩展的顺序是：
brace  expansion,  tilde expansion, parameter, variable 和 arithmetic expansion 还有 command substitution  (按照从左到右的顺序),  word  splitting,  
最后是 pathname expansion.  
还有一种附加的扩展：process subtitution (进程替换) 只有在支持它的系统中有效(POSIX不支持)。  
       
只有    brace   expansion,   word   splitting,   和   pathname   expansion  在扩展前后的词数会发生改变；其他扩展总是将一个词扩展为一个词。
唯一的例外是  "$@"  和  "${name[@]}"  (参见PARAMETERS参数)。  




