---
title:  "MySQL存储过程"
date: 2017-05-10  12:00:00
updated: 2017-05-10  12:00:00
comments: true
categories: 数据库
tags:
    - MySQL
---

# 优缺点

### 优点
* 在生产环境下，可以通过直接修改存储过程的方式修改业务逻辑（或bug），而不用重启服务器。但这一点便利被许多人滥用了。有人直接就在正式服务器上修改存储过程，而没有经过完整的测试，后果非常严重。
* 执行速度快。存储过程经过编译之后会比单独一条一条执行要快。但这个效率真是没太大影响。如果是要做大数据量的导入、同步，我们可以用其它手段。
* 减少网络传输。存储过程直接就在数据库服务器上跑，所有的数据访问都在服务器内部进行，不需要传输数据到其它终端。但我们的应付服务器通常与数据库是在同一内网，大数据的访问的瓶颈会是硬盘的速度，而不是网速。
* 能够解决presentation与数据之间的差异，说得文艺青年点就是解决OO模型与二维数据持久化之间的阻抗。领域模型和数据模型的设计可能不是同一个人（一个是SA，另一个是DBA），两者的分歧可能会很大——这不奇怪，一个是以OO的思想来设计，一个是结构化的数据来设计，大家互不妥协——你说为了软件的弹性必须这么设计，他说为了效率必须那样设计，为了抹平鸿沟，就用存储过程来做数据存储的逻辑映射（把属性映射到字段）。好吧，台下已经有同学在叨咕ORM了。
* 方便DBA优化。所有的SQL集中在一个地方，DBA会很高兴。这一点算是ORM的软肋。不过按照CQRS框架的思想，查询是用存储过程还是ORM，还真不是问题——DBA对数据库的优化，ORM一样会受益。况且放在ORM中还能用二级缓存，有些时候效率还会更高。
     <!-- more -->

### 缺点
* SQL本身是一种结构化查询语言，加上了一些控制（赋值、循环和异常处理等），但不是OO的，本质上还是过程化的，面对复杂的业务逻辑，过程化的处理会很吃力。这一点算致命伤。
* 不便于调试。基本上没有较好的调试器，很多时候是用print来调试，但用这种方法调试长达数百行的存储过程简直是噩梦。好吧，这一点不算啥，C#/Java一样能写出噩梦般的代码。
* 没办法应用缓存。虽然有全局临时表之类的方法可以做缓存，但同样加重了数据库的负担。如果缓存并发严重，经常要加锁，那效率实在堪忧。
* 无法适应数据库的切割（水平或垂直切割）。数据库切割之后，存储过程并不清楚数据存储在哪个数据库中.

     
# 基本语法

### 一.创建存储过程
```sql
create procedure sp_name()
begin
......
end
```
### 二.调用存储过程
* 基本语法：**call sp_name()**
* 注意：存储过程名称后面必须加括号，哪怕该存储过程没有参数传递

### 三.删除存储过程
* 基本语法：**drop procedure sp_name()**
* 注意事项: 不能在一个存储过程中删除另一个存储过程，只能调用另一个存储过程

### 四.其他常用命令

* **show procedure status**
* 显示数据库中所有存储的存储过程基本信息，包括所属数据库，存储过程名称，创建时间等

* **show create procedure sp_name**
* 显示某一个MySQL存储过程的详细信息


## 数据类型及运算符

### 一、基本数据类型

### 二、变量：

自定义变量：**DECLARE   a INT ; SET a=100;**

可用以下语句代替：**DECLARE a INT DEFAULT 100;**

变量分为**用户变量**和**系统变量**，**系统变量**又分为**会话**和**全局级变量**

**用户变量**：用户变量名一般以@开头，滥用用户变量会导致程序难以理解及管理

* 1、 在mysql客户端使用用户变量
```sql
mysql> SELECT 'Hello World' into @x;
mysql> SELECT @x;

mysql> SET @y='Goodbye Cruel World';
mysql> select @y;

mysql> SET @z=1+2+3;
mysql> select @z;
```

* 2、 在存储过程中使用用户变量
```sql
mysql> CREATE PROCEDURE GreetWorld( ) SELECT CONCAT(@greeting,' World');
mysql> SET @greeting='Hello';
mysql> CALL GreetWorld( );
```

* 3、 在存储过程间传递全局范围的用户变量
```sql
mysql> CREATE PROCEDURE p1( )   SET @last_procedure='p1';
mysql> CREATE PROCEDURE p2( ) SELECT CONCAT('Last procedure was ',@last_procedure);
mysql> CALL p1( );
mysql> CALL p2( );
```

### 三、运算符：
* 1.算术运算符

~~~
+     加   SET var1=2+2;       4
-     减   SET var2=3-2;       1
*     乘   SET var3=3*2;       6
/     除   SET var4=10/3;      3.3333
DIV   整除 SET var5=10 DIV 3;  3
%     取模 SET var6=10%3 ;     1
~~~
* 2.比较运算符

~~~
>            大于 1>2 False
<            小于 2<1 False
<=           小于等于 2<=2 True
>=           大于等于 3>=2 True
BETWEEN      在两值之间 5 BETWEEN 1 AND 10 True
NOT BETWEEN  不在两值之间 5 NOT BETWEEN 1 AND 10 False
IN           在集合中 5 IN (1,2,3,4) False
NOT IN       不在集合中 5 NOT IN (1,2,3,4) True
=            等于 2=3 False
<>, !=       不等于 2<>3 False
<=>          严格比较两个NULL值是否相等 NULL<=>NULL True
LIKE         简单模式匹配 "Guy Harrison" LIKE "Guy%" True
REGEXP       正则式匹配 "Guy Harrison" REGEXP "[Gg]reg" False
IS NULL      为空 0 IS NULL False
IS NOT NULL  不为空 0 IS NOT NULL True
~~~
* 3.逻辑运算符

* 4.位运算符

~~~
|   或
&   与
<<  左移位
>>  右移位
~   非(单目运算，按位取反)
~~~
**注释：**
/* 注释内容 */ 一般用于多行注释

# 流程控制
### 一、顺序结构
### 二、分支结构
```sql
if 条件 then
else 
end if;
```
### 三、循环结构
**while循环**
```sql
SET counter = 0;   
WHILE counter != 10 DO   
    SET counter = counter+1;   
END WHILE;   
```
**loop循环**
```sql
SET counter = 0;   
my_simple_loop: LOOP   
    SET counter = counter+1;   
    IF counter = 10 THEN   
        LEAVE my_simple_loop;   
    END IF;   
END LOOP my_simple_loop;   
```
**repeat until循环**
```sql
SET counter = 0;   
REPEAT   
    SET counter = counter+1;   
UNTIL counter = 10 END REPEAT;   
```
**注：**
区块定义，常用
~~~
begin
......
end;
~~~
也可以给区块起别名，如：
~~~
lable:begin
...........
end lable;
~~~
可以用leave lable;

跳出区块，执行区块以后的代码 begin和end如同C语言中的{ 和 }。

# 输入和输出

mysql存储过程的参数用在存储过程的定义，共有三种参数类型,**IN,OUT,INOUT**
~~~
Create procedure|function([[IN |OUT |INOUT ] 参数名 数据类形...])
~~~
**IN 输入参数**
* 表示该参数的值必须在调用存储过程时指定，在存储过程中修改该参数的值不能被返回，为默认值

**OUT 输出参数**
* 该值可在存储过程内部被改变，并可返回

**INOUT 输入输出参数**
* 调用时指定，并且可被改变和返回

**IN参数例子：**
```sql
CREATE PROCEDURE sp_demo_in_parameter(IN p_in INT)
BEGIN
SELECT p_in; /*查询输入参数*/
SET p_in=2; /*修改*/
select p_in;/*查看修改后的值*/
END;
执行结果:
mysql> set @p_in=1
mysql> call sp_demo_in_parameter(@p_in)
略
mysql> select @p_in;
略
```
以上可以看出，p_in虽然在存储过程中被修改，但并不影响@p_id的值

**OUT参数例子**
```sql
CREATE PROCEDURE sp_demo_out_parameter(out p_in INT)
BEGIN
SELECT p_in;
SET p_in=2; 
select p_in;
END;
执行结果:
mysql> set @p_in=1
mysql> call sp_demo_out_parameter(@p_in)
略
mysql> select @p_in;
略
```
**INOUT参数例子：** 
```sql
CREATE PROCEDURE sp_demo_inout_parameter(inout p_in INT)
BEGIN
SELECT p_in;
SET p_in=2; 
select p_in;
END;
执行结果:
mysql> set @p_in=1
mysql> call sp_demo_inout_parameter(@p_in)
略
mysql> select @p_in;
略
```

# 函数库

mysql存储过程基本函数包括：**字符串类型**，**数值类型**，**日期类型**

### 一、字符串类
~~~
CHARSET(str) //返回字串字符集
CONCAT (string2 [,… ]) //连接字串
INSTR (string ,substring ) //返回substring首次在string中出现的位置,不存在返回0
LCASE (string2 ) //转换成小写
LEFT (string2 ,length ) //从string2中的左边起取length个字符
LENGTH (string ) //string长度
LOAD_FILE (file_name ) //从文件读取内容
LOCATE (substring , string [,start_position ] ) 同INSTR,但可指定开始位置
LPAD (string2 ,length ,pad ) //重复用pad加在string开头,直到字串长度为length
LTRIM (string2 ) //去除前端空格
REPEAT (string2 ,count ) //重复count次
REPLACE (str ,search_str ,replace_str ) //在str中用replace_str替换search_str
RPAD (string2 ,length ,pad) //在str后用pad补充,直到长度为length
RTRIM (string2 ) //去除后端空格
STRCMP (string1 ,string2 ) //逐字符比较两字串大小,
SUBSTRING (str , position [,length ]) //从str的position开始,取length个字符,
注：mysql中处理字符串时，默认第一个字符下标为1，即参数position必须大于等于1
mysql> select substring(’abcd’,0,2);
+———————–+
| substring(’abcd’,0,2) |
+———————–+
|                       |
+———————–+
1 row in set (0.00 sec)

mysql> select substring(’abcd’,1,2);
+———————–+
| substring(’abcd’,1,2) |
+———————–+
| ab                    |
+———————–+
1 row in set (0.02 sec)

TRIM([[BOTH|LEADING|TRAILING] [padding] FROM]string2) //去除指定位置的指定字符
UCASE (string2 ) //转换成大写
RIGHT(string2,length) //取string2最后length个字符
SPACE(count) //生成count个空格
~~~
### 二、数值类型
~~~
ABS (number2 ) //绝对值
BIN (decimal_number ) //十进制转二进制
CEILING (number2 ) //向上取整
CONV(number2,from_base,to_base) //进制转换
FLOOR (number2 ) //向下取整
FORMAT (number,decimal_places ) //保留小数位数
HEX (DecimalNumber ) //转十六进制
注：HEX()中可传入字符串，则返回其ASC-11码，如HEX(’DEF’)返回4142143
也可以传入十进制整数，返回其十六进制编码，如HEX(25)返回19
LEAST (number , number2 [,..]) //求最小值
MOD (numerator ,denominator ) //求余
POWER (number ,power ) //求指数
RAND([seed]) //随机数
ROUND (number [,decimals ]) //四舍五入,decimals为小数位数]

注：返回类型并非均为整数，如：

(1)默认变为整形值
mysql> select round(1.23);
+————-+
| round(1.23) |
+————-+
|           1 |
+————-+
1 row in set (0.00 sec)

mysql> select round(1.56);
+————-+
| round(1.56) |
+————-+
|           2 |
+————-+
1 row in set (0.00 sec)

(2)可以设定小数位数，返回浮点型数据

mysql> select round(1.567,2);
+—————-+
| round(1.567,2) |
+—————-+
|           1.57 |
+—————-+
1 row in set (0.00 sec)

SIGN (number2 ) //返回符号,正负或0
SQRT(number2) //开平方
~~~
### 三、日期类型
~~~
ADDTIME (date2 ,time_interval ) //将time_interval加到date2
CONVERT_TZ (datetime2 ,fromTZ ,toTZ ) //转换时区
CURRENT_DATE ( ) //当前日期
CURRENT_TIME ( ) //当前时间
CURRENT_TIMESTAMP ( ) //当前时间戳
DATE (datetime ) //返回datetime的日期部分
DATE_ADD (date2 , INTERVAL d_value d_type ) //在date2中加上日期或时间
DATE_FORMAT (datetime ,FormatCodes ) //使用formatcodes格式显示datetime
DATE_SUB (date2 , INTERVAL d_value d_type ) //在date2上减去一个时间
DATEDIFF (date1 ,date2 ) //两个日期差
DAY (date ) //返回日期的天
DAYNAME (date ) //英文星期
DAYOFWEEK (date ) //星期(1-7) ,1为星期天
DAYOFYEAR (date ) //一年中的第几天
EXTRACT (interval_name FROM date ) //从date中提取日期的指定部分
MAKEDATE (year ,day ) //给出年及年中的第几天,生成日期串
MAKETIME (hour ,minute ,second ) //生成时间串
MONTHNAME (date ) //英文月份名
NOW ( ) //当前时间
SEC_TO_TIME (seconds ) //秒数转成时间
STR_TO_DATE (string ,format ) //字串转成时间,以format格式显示
TIMEDIFF (datetime1 ,datetime2 ) //两个时间差
TIME_TO_SEC (time ) //时间转秒数]
WEEK (date_time [,start_of_week ]) //第几周
YEAR (datetime ) //年份
DAYOFMONTH(datetime) //月的第几天
HOUR(datetime) //小时
LAST_DAY(date) //date的月的最后日期
MICROSECOND(datetime) //微秒
MONTH(datetime) //月
MINUTE(datetime) //分
~~~
* 注：可用在INTERVAL中的类型：**DAY ,DAY_HOUR ,DAY_MINUTE ,DAY_SECOND ,HOUR ,HOUR_MINUTE ,HOUR_SECOND ,MINUTE ,MINUTE_SECOND,MONTH ,SECOND ,YEAR**
```sql
DECLARE variable_name [,variable_name...] datatype [DEFAULT value];
```
其中，datatype为mysql的数据类型，如:**INT, FLOAT, DATE, VARCHAR(length)**

**实例：**
```sql
DECLARE l_int INT unsigned default 4000000;
DECLARE l_numeric NUMERIC(8,2) DEFAULT 9.95;
DECLARE l_date DATE DEFAULT '1999-12-31';
DECLARE l_datetime DATETIME DEFAULT '1999-12-31 23:59:59';
DECLARE l_varchar VARCHAR(255) DEFAULT 'This will not be padded';
```

