# 数组

## 01.数组定义

在 awk 中数组叫做关联数组(associative arrays)，因为下标记可以是数也可以是串。
awk 中的数组不必提前声明，也不必声明大小，数组元素用 0 或空串来初始化，这根据上下文而定。

定义方法
1：可以用数值作数组索引(下标)
Tarray[1]=“cheng mo”
Tarray[2]=“800927”

2：可以用字符串作数组索引(下标)
Tarray[“first”]=“cheng ”
Tarray[“last”]=”mo”
Tarray[“birth”]=”800927”

使用中 print Tarray[1] 将得到”cheng mo” 而 print Tarray[2] 和 Tarray[“birth”] 都将得到 ”800927” 。

## 02.数组操作

访问数组的元素：
经常使用循环来访问数组元素，下面是一种循环类型的基本结构:
for (element in array_name ) print array_name[element] 
当for使用in形式时，awk将数组的下标依次赋值给循环控制变量，每次赋值都执行一遍循环体。
缺点是，当awk以这种形式在数组下标之间轮转时，它不会依照任何特定的顺序。
举例：
awk 'BEGIN{  print split("123#456#789",mya,"#") ; for ( i in mya ) {  print mya[i]  } } ' 
注意：遍历数组打印的序列是无序的，即使数组在定义时，索引值是有序的。
-------------------------------------------------------------------------------------------------------
获取数组长度(length方法)
$ awk 'BEGIN{info="it is a test";lens=split(info,tA," ");print length(tA),lens;}'
4 4
length返回字符串以及数组长度，split进行分割字符串为数组，也会返回分割得到数组长度。
-------------------------------------------------------------------------------------------------------
删除数组或数组键值：
$ awk 'BEGIN{tB["a"]="a1";tB["b"]="b1";delete tB["a"];for(k in tB){print k,tB[k];}}'                     
b b1
delete array[key]    #可以删除，对应数组key的序列值。
如果 delete array 可以删除整个数组，但不会删除数组名，如果数组名仍然不能用于其它变量名。
-------------------------------------------------------------------------------------------------------
判断键值存在：
一个错误的判断方法：
$ awk 'BEGIN{tB["a"]="a1";tB["b"]="b1";if(tB["c"]!="1"){print "no found";};for(k in tB){print k,tB[k];}}' 
no found
a a1
b b1
c
以上出现奇怪问题，tB["c"]没有定义，但是循环时候，发现已经存在该键值，它的值为空.
这里需要注意，awk数组是关联数组，只要通过数组引用它的key，就会自动创建改序列。
 
正确判断方法：
$ awk 'BEGIN{tB["a"]="a1";tB["b"]="b1";if( "c" in tB){print "ok";};for(k in tB){print k,tB[k];}}'  
a a1
b b1

if ( key in array ) 通过这种方法判断数组中是否包含”key”键值。
也可以使用 if ( ! ( key in array ) )  来判断。

## 03.多维数组（多索引数组）

awk的多维数组在本质上是一维数组，更确切一点，awk在存储上并不支持多维数组。
awk提供了逻辑上模拟二维数组的访问方式，例如：array[2,4] = 1这样的访问是允许的。
awk使用一个特殊的字符串SUBSEP (\034)作为分割字段，在上面的例子中，关联数组array存储的键值实际上是2\0344。
------------------------------------------------------------------------------------------------------------
类似一维数组的成员测试，多维数组可以使用 if ( (i,j) in array)这样的语法，但是下标必须放置在圆括号中。
类似一维数组的循环访问，多维数组使用 for ( item in array )这样的语法遍历数组。
与一维数组不同的是，多维数组必须使用split()函数来访问单独的下标分量。split ( item, subscr, SUBSEP)
 
$ awk 'BEGIN{
       for(i=1;i<=9;i++)
       {
          for(j=1;j<=9;j++)  
          {
              tarr[i,j]=i*j;
              print i,"*",j,"=",tarr[i,j];
          }
       }
      }'
1 * 1 = 1
1 * 2 = 2
1 * 3 = 3
1 * 4 = 4
1 * 5 = 5
1 * 6 = 6
......
可以通过array[k1,k2]引用获得数组内容。
---------------------------------------------------------------
$ awk 'BEGIN{
for(i=1;i<=9;i++)
{
  for(j=1;j<=9;j++)  
  {
      tarr[i,j]=i*j;
  }
}
for( m in tarr )              # 遍历多索引数组
{
    split(m,tarr2,SUBSEP);
    print tarr2[1],"*",tarr2[2],"=",tarr[m];
}
}'
## 04.数组排序

### 01.内置函数asort/asorti

asort 是对数组的值进行排序，并且会丢掉原先键值。
asorti是对数组的下标进行排序。
------------------------------------------------
数据文件：
12 34
78 90
23 45
awk是关联数组，for…in循环输出时候，默认打印出来是无序数组：
# awk '{a[$1]=$2}END{for(i in a) print i,a[i]}' test.txt 
78 90
12 34
23 45
----------------------------------------------------------------------------------------------------------
asort
# awk '{ a[$1]=$2 }
END{
print "Before sort:"; 
for(i in a) print i"\t"a[i]; 
asort(a); 
print "After sort:"; 
for(i in a) print i"\t"a[i] } ' test.txt    
         
Before sort:
78      90
12      34
23      45
After sort:
1       34
2       45
3       90

# awk '{a[$1]=$2}END{print "Before sort arry a:"; for(i in a) print i"\t"a[i]; asort(a,b); print "After sort arry a:"; for(i in a) print i"\t"a[i]; print "Arry b:";  for (i in b) print i"\t"b[i]}' test.txt  
Before sort arry a:
78      90
12      34
23      45
After sort arry a:
78      90
12      34
23      45
Arry b:
1       34
2       45
3       90
----------------------------------------------------------------------------------------------------------
asorti 
# awk ' { a[$1]=$2 }
END {
print "Before sort:";  
for(i in a) print i"\t"a[i]; 
asorti(a); 
print "After sort:"; 
for(i in a) print i"\t"a[i] } '   test.txt 

Before sort:
78      90
12      34
23      45
After sort:
1       12
2       23
3       78   

#awk '  { a[$1]=$2 } 
END {
print "Before sort arry a:"; 
for( i in a )   print i"\t"a[i]; 
asorti(a,b); 
print "After sort arry a:";  
for(i in a)  print i"\t"a[i];  
print "Arry b:";  
for (i in b) print i"\t"b[i]}  '      test.txt
  
Before sort arry a:
78      90
12      34
23      45
After sort arry a:
78      90
12      34
23      45
Arry b:
1       12
2       23
3       78
----------------------------------------------------------------------------------------------------------

### 02.管道发送到sort排序

$ awk 'BEGIN
{
  a[100]=100;
  a[2]=224;
  a[3]=34;
  for(i in a) {
  print i,a[i] | "sort -r -n -k2";
  }
}'
2 224
100 100
3 34
 
通过管道，发送到外部程序“sort”排序，-r 从大到小，-n 按照数字排序，-k2 以第2列排序。

### 03.自定义排序函数

#arr               传入一维数组
#key               排序类型 1是按照值排序 2按照键值
#datatype          比较类型 1按照数字排序 2按照字符串排序
#tarr              排序返回的数组
#splitseq          分割字符串 数组中键与值之间分割字符串
#return            数组长度
#实现思路，将原始数组a[‘a’]=100 排序后变成 a[1]=a分隔符100 ，然后按照下标递归显示内容。 。
-----------------------------------------------------------------------------------------------------------------------
冒泡排序：
function sortArr(arr,key,datatype,tarr,splitseq)
{
    if(key ~ /[^1-2]/) 
    {return tarr;}
    for(k in arr)
    {
      tarr[++alen]=(k""splitseq""arr[k]);
    }
    for(m=1;m<=alen;m++)
    {
        for(n=1;n<=alen-m-1;n++)
        {
            split(tarr[m],tm,splitseq);
            split(tarr[n+1],tn,splitseq);
                tnum=tarr[m];
            if(datatype==1)
            {
                if(tm[key]+0<tn[key]+0)
                {
                     tarr[m]=tarr[n+1];
                     tarr[n+1]=tnum;
                }
            }
            else
            {
                if((tm[key]"") < (tn[key]""))
                {
                     tarr[m]=tarr[n+1];
                     tarr[n+1]=tnum;
                }
            }
        }
    }
    return alen;
}
-----------------------------------------------------------------------------------------------------------------------
完整代码如下：
$ awk 'BEGIN{
a["a"]=100;
a["b"]=110;
a["c"]=10;
splitseq="%%";
alen=sortArr(a,2,1,tarr,splitseq);
for(m=1;m<=alen;m++)
{
    split(tarr[m],ta,splitseq);
    print m,ta[1],ta[2];
}
}
function sortArr(arr,key,datatype,tarr,splitseq)
{
    if(key ~ /[^1-2]/) 
    {return tarr;}
    for(k in arr)
    {
      tarr[++alen]=(k""splitseq""arr[k]);
    }
    for(m=1;m<=alen;m++)
    {
        for(n=1;n<=alen-m-1;n++)
        {
            split(tarr[m],tm,splitseq);
            split(tarr[n+1],tn,splitseq);
                tnum=tarr[m];
            if(datatype==1)
            {
                if(tm[key]+0<tn[key]+0)
                {
                     tarr[m]=tarr[n+1];
                     tarr[n+1]=tnum;
                }
            }
            else
            {
                if((tm[key]"") < (tn[key]""))
                {
                     tarr[m]=tarr[n+1];
                     tarr[n+1]=tnum;
                }
            }
        }
    }
    return alen;
}
'
1 b 110
2 a 100
3 c 10
-----------------------------------------------------------------------------------------------------------------------

### 10.按索引有序打印数组

由于awk数组，是关联数组。for…in循环输出时候，默认打印出来是无序数组：
$ awk 'BEGIN{ info = "this is a test"; split(info,tA," "); for ( k in tA ) { print k,tA[k]; } }'
4 test
1 this
2 is
3 a
------------------------------------------------------------------------------------------------------------
如果需要按照顺序输出，通过键值定位方式输出：
$ awk 'BEGIN { info = "this is a test";  slen = split( info, tA, " "); for ( i=1; i<=slen; i++) { print i,tA[i]; } }'       
1 this
2 is
3 a
4 test
