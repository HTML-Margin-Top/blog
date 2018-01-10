---
title: mysql创建function遇到的问题
date: 2018-01-10 16:29:14
tags:
---
今天导数据库，最后几个function导入失败
报错信息为：Cannot load from mysql.proc. The table is probably corrupted
问题分析：MySQL升级完成后未对相关数据库执行升级。LINUX下将mysql从5.1升级至5.5后，发现存储过程不能用了。创建和使用存储过程时就会提示Cannot load from mysql.proc. The table is probably corrupted。
解决办法：执行 mysql_upgrade -uroot -p 命令即可

解决掉版本问题之后，又发现了新的问题。
导入数据的时候遇见报错：This function has none of DETERMINISTIC, NO SQL, or READS SQL DATA in its declaration and binary logging is enabled (you *might* want to use the less safe log_bin_trust_function_creators variable)
问题分析：
这是我们开启了bin-log, 我们就必须指定我们的函数是否是
1. DETERMINISTIC 不确定的
2. NO SQL 没有SQl语句，当然也不会修改数据
3. READS SQL DATA 只是读取数据，当然也不会修改数据
4. MODIFIES SQL DATA 要修改数据
5. CONTAINS SQL 包含了SQL语句
其中在function里面，只有 DETERMINISTIC, NO SQL 和 READS SQL DATA 被支持。如果我们开启了 bin-log, 我们就必须为我们的function指定一个参数。
解决方法如下：
1. mysql> set global log_bin_trust_function_creators = 1;
2. 系统启动时 --log-bin-trust-function-creators=1
3. 在my.ini(linux下为my.conf)文件中 [mysqld] 标记后加一行内容为 log-bin-trust-function-creators=1
