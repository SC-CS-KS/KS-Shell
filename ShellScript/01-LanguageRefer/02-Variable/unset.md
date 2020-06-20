# unset

#type unset
unset is a shell builtin
------------------------------------------------------------------------------------------------
用途：
从当前Shell删除变量或函数
-f  指定删除函数
-v  指定删除变量，没有选项时，该选项为默认行为模式。
------------------------------------------------------------------------------------------------
unset: unset [-f] [-v] [name ...]
    
For each NAME, remove the corresponding variable or function.  
Given the `-v', unset will only act on variables.  
Given the `-f' flag, unset will only act on functions.  

With neither flag, unset first tries to unset a variable, and if that fails, then tries to unset a function.  
Some variables cannot be unset; also see readonly.
