---
title: MySql知识积累
date: 2018-01-07 23:22:55
tags:
---
> 如果你认识以前的我，也许会原谅现在的我。----张爱玲
<style>myclass{margin-left:2em}</style>

<!-- more -->
## TRIM()函数
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
 * * *

## 时间相关
-   CURRENT_TIMESTAMP   当前时间
-   DATE()  取日期
-   NOW() 取当前时间
-  DATEDIFF(a，b)    时间相减
* * *

## FIND_IN_SET(str,strlist)
假如字符串str 在由N个子链组成的字符串列表strlist 中，则返回值为str在字符串列表中的位置（从1开始）。
此时可以理解为Java中List类的indexOf()方法。
一个字符串列表就是一个由一些被‘,’符号分开的自链组成的字符串。
如果第一个参数是一个常数字符串，而第二个是type SET列，则   FIND_IN_SET() 函数被优化，使用比特计算。
如果str不在strlist 或strlist 为空字符串，则返回值为 0 。如任意一个参数为NULL，则返回值为 NULL。
这个函数在第一个参数包含一个逗号(‘,’)时将无法正常运行。
```bash
mysql> SELECT FIND_IN_SET('b','a,b,c,d');
2
```
FIND_IN_SET 是精确匹配，字段值以英文 ‘,’。
Like 是广泛的模糊查询，字段中无需使用分隔符。
两者基于使用场景自行选择即可。
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
 -  TRUNC(value,precision)按精度(precision)截取某个数字,不进行舍入操作
 -  ROUND(value,precision)根据给定的精度(precision)输入数值
     >  ROUND(a) 四舍五入到整数
     >  ROUND(a，b)四舍五入到小数点后第b位
 -  CEIL (value) 产生大于或等于指定值(value)的最小整数。向上取整
 -  FLOOR（value）与 ceil()相反，产生小于或等于指定值(value)的最小整数。向下取整
 -  SIGN(value) 与绝对值函数ABS()相反。ABS()给出的是值的量而不是其符号，SIGN(value)则给出值的符号而不是量。
* * *

## 变量
- 局部变量
    局部变量一般用在sql语句块中，比如存储过程的begin/end。其作用域仅限于该语句块，在该语句块执行完毕后，局部变量就消失了。
    局部变量一般用declare来声明，可以使用default来说明默认值。也可以用SET来进行初始化。
    例如在存储过程中定义局部变量：
    ```bash
    BEGIN
      DECLARE sTmpID VARCHAR(4000) DEFAULT "$";
      DECLARE sTmpChileID VARCHAR(4000);

      SET sTmpChileID = cast(roleID as CHAR);

      WHILE sTmpChileID IS NOT NULL DO
        SET sTmpID = concat(sTmpID,',',sTmpChileID);
        SELECT group_concat(TRole.RoleID) INTO  sTmpChileID FROM TRole WHERE find_in_set(ParentID,sTmpChileID)>0;
      END WHILE;
      RETURN sTmpID;
    END
    ```
    上述代码中sTmpID、sTmpChileID为局部变量

-   用户变量
    用户变量：以"@"开始，形式为"@变量名"
    用户变量的作用域要比局部变量要广。用户变量可以作用于当前整个连接，但是当当前连接断开后，其所定义的用户变量都会消失。
    用户变量使用如下(这里我们无须使用declare关键字进行定义，可以直接这样使用):
    select @变量名
    对用户变量赋值有两种方式，一种是直接用"="号，另一种是用":="号。
    其区别在于使用set命令对用户变量进行赋值时，两种方式都可以使用；当使用select语句对用户变量进行赋值时，只能使用":="方式，**因为在select语句中，"="号被看作是比较操作符。**
    示例程序如下：
    ```bash
    SELECT
    T.*,
    (@rowNO := @rowNo+1) AS displayNo
    FROM
        (SELECT
        IFNULL(TR.VisitCode, "") AS recordID,
        IFNULL(TR.VisitDate, "") AS visitDate,
        IFNULL(TD.JobCode, "") AS jobCode,
        IFNULL(TR.APN, "") AS apn,
        IFNULL(TD.DepartmentCode, "") AS departmentCode,
        IFNULL(TP.Name, "") AS patientName,
        IFNULL(TP.Mobile, "") AS mobile,
        IFNULL(TR.RefuseRemark, "") AS refuseRemark,
        IFNULL(TP.IDCardNo, "") AS idCardNo,
        IFNULL(TP.Sex, "") AS patientSex,
        IFNULL(TP.Birthday, "") AS patientBirthday,
        IFNULL(TR.DoctorConfirmTime, "") AS doctorConfirmTime,
        IFNULL(TD.DoctorID, "") AS doctorID,
        IFNULL(TR.ClinicCode, "0") AS  job
        FROM	TRegistration TR
        LEFT JOIN TDoctor TD ON TD.DoctorID = TR.DoctorID
        LEFT JOIN TPatient TP ON TP.PatientID = TR.PatientID
        WHERE TR.RegistrationStatus = '10'
        ) T,(select @rowNO :=0) b
    WHERE T.VisitDate = #{visitDate}
    ORDER BY T.DoctorConfirmTime
    ```
    首先使用`(select @rowNO :=0) b`声明并初始化，
    然后`(@rowNO := @rowNo+1) AS displayNo`使用其作为行号
* * *

## > 与 >=的执行效率
`>=`先通过索引快速定位到等于项，然后在向下扫描，选出大于项；
而`>`不会通过索引直接定位，可能会走全盘扫描；
