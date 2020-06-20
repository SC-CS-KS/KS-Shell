# 99.Shell Misc

history | 
awk '{
    CMD[$2]++;count++;
} 
END { 
    for (a in CMD )
      print CMD[a] " " CMD[a]/count*100 "% " a
 }' | 
grep -v "./" | 
column -c3 -s " " -t | 
sort -nr | 
nl | 
head -n10

陈皓注：
history| awk '{print $2}' |awk 'BEGIN{FS="|"}{print $1}' |sort|uniq -c| sort -nr | head -10

这行脚本能输出你最常用的十条命令，由此甚至可以洞察你是一个什么类型的程序员。
-------------------------------------------------------------------------------------------------------------------
