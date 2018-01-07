---
title: MySql知识积累
date: 2018-01-07 23:22:55
tags:
---
> 如果你认识以前的我，也许会原谅现在的我。----张爱玲


## trim()函数
用法：
 TRIM([{BOTH | LEADING | TRAILING} [remstr] FROM] str)
 TRIM([remstr FROM] str)
 返回字符串str，删除所有 remstr 前缀或后缀。如果没有符 BOTH, LEADING, 或TRAILING给出，假定则是BOTH(默认的)。
 remstr为可任选的，如果未指定，空格被删除。

 默认过滤空格，BOTH参数
```bash
TRIM("   hello,world   ");
hello,world
```
过滤前缀指定字符
````bash
TRIM(LEADING 'x' FROM 'xxxbarxxx')
barxxx
```
过滤两端字符
````bash
TRIM(BOTH 'x' FROM 'xxxbarxxx')
bar
```
过滤后缀指定字符
````bash
TRIM(TRAILING 'xyz' FROM 'barxxyz')
barx
```
删除前缀空格
````bash
LTRIM('  barbar')
barbar
```
删除后缀空格
````bash
RTRIM('barbar   ')
barbar
 ```
 * * *
## 清空表数据
TRUNCATE TABLE TABLE_NAME;
* * *
## COUNT
+   COUNT(*):聚合统计所有列
-   COUNT(1):聚合统计第一列
*   COUNT([列名]):聚合统计指定列
* * *
##   HIVING
跟在group by 和 order by 之后的 可以按结果筛查

## mybatis 运算符
&lt;	<	小于号
&gt;	>	大于号
&amp;	&	与
&apos;	'	单引号
&quot;	"	双引号
* * *
## 时间相关
-   CURRENT_TIMESTAMP   当前时间
-   DATE()  取日期
-   NOW() 取当前时间
-  DATEDIFF(a，b)    时间相减
##  choose与when
* * *
## FIND_IN_SET()
* * *
## NULL相关
-   IFNULL(expr1,expr2)
    如果 expr1 不是 NULL，IFNULL() 返回 expr1，否则它返回 expr2。
    ```bash
     mysql> IFNULL(1,0)
    1
    ```
-   NULLIF(expr1,expr2)
    如果expr1 = expr2 成立，那么返回值为NULL，否则返回值为expr1。这和CASE  WHEN expr1 = expr2 THEN NULL ELSE   expr1 END相同。
     ```bash
         mysql> IFNULL(1,0)
        1
        ```
-  ISNULL(expr)
    如expr 为null，那么isnull() 的返回值为 1，否则返回值为 0。
     ```bash
             mysql> ISNULL(1+1)
            0
            ```
* * *
## 取整函数
 -  TRUNC（value,precision）按精度(precision)截取某个数字,不进行舍入操作
 -  ROUND（value,precision）根据给定的精度(precision)输入数值
     >  ROUND(a) 四舍五入到整数
     >  ROUND(a，b)四舍五入到小数点后第b位
 -  CEIL (value) 产生大于或等于指定值（value）的最小整数。向上取整
 -  FLOOR（value）与 ceil（）相反，产生小于或等于指定值（value）的最小整数。向下取整
 -  SIGN(value) 与绝对值函数ABS()相反。ABS()给出的是值的量而不是其符号，SIGN(value)则给出值的符号而不是量。
