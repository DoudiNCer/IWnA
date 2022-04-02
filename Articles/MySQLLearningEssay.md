# MySQL学习笔记

## MySQL介绍

&emsp;&emsp;MySQL是一种流行的**关系型数据库管理系统**(RDBMS)，除NySQL以外，还有其他数据库管理系统：

- **关系型数据库**：表与表之间有复杂的逻辑关系，如SQL Server,SQLite...
- **非关系型数据库**：将数据简化为key-value,避免数据冗余，如MongoDB.Redis...

&emsp;&emsp;在Orcale收购MySQL后，有将其闭源的风险，因此原开发团队开发了其分支版本——MariaDB并全面兼容MySQL 5.x。

## MySQL在Linux下的安装和初始化

### MySQL的组成

> - **MySQL** - MySQL服务器程序
> - **MySQL-client** - MySQL 客户端
> - **MySQL-devel** - ~~库和包含文件，如果你想要编译其它MySQL客户端，例如Perl模块，则需要安装该RPM包。~~ 这个玩过Linux的应该都知道
> - **MySQL-shared** - 该软件包包含某些语言和应用程序需要动态装载的共享库(libmysqlclient.so*)。
> - **MySQL-bench** - MySQL数据库服务器的基准和性能测试工具。

### MySQL的安装

（自觉用root，不会的命令自觉man一下）

下载安装（RHEL系）：

```shell
wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm
rpm -ivh mysql-community-release-el7-5.noarch.rpm
yum update
yum install mysql-server
```

权限配置：

```shell
chown -R mysql:mysql /var/lib/mysql
```

初始化及启动：

```shell
mysqld --initialize
systemctl start mysqld
systemctl enable mysqld		# 酌情使用
mysqladmin --version		# 验证安装
```

或者用Docker：

```shell
docker run -itd --name=$containerName -p $hostPort:3306 -e MYSQL_ROOT_PASSWORD=$rootPassword mysql:5
```

用户登录：

```shell
mysql -h $serverLocate -P $port(default 3306) -u $username -p $password
```

## MySQL专业术语

> - **数据库**：数据库是一些关联表的集合。
> - **数据表**：表是数据的矩阵。在一个数据库中的表看起来像一个简单的电子表格。
> - **列**：一列(数据元素) 包含了相同类型的数据, 例如邮政编码的数据。
> - **行**：一行（元组，或记录）是一组相关的数据，例如一条用户订阅的数据。
> - **冗余**：存储两倍数据，冗余降低了性能，但提高了数据的安全性。
> - **主键**：主键是唯一的。一个数据表中只能包含一个主键。你可以使用主键来查询数据。
> - **外键**：外键用于关联两个表。
> - **复合键**：复合键（组合键）将多个列作为一个索引键，一般用于复合索引。
> - **索引**：使用索引可快速访问数据库表中的特定信息。索引是对数据库表中一列或多列的值进行排序的一种结构。类似于书籍的目录。
> - **参照完整性**：参照的完整性要求关系中不允许引用不存在的实体。与实体完整性是关系模型必须满足的完整性约束条件，目的是保证数据的一致性。

## 数据操作

> 这里有几个坑：表名、列名用反引号“``”，数据用单引号“''”，NULL不要用引号。

### 库

```mysql
show databases;
show create database $database;		# 查看已创建数据库的信息
select database() from dual;		# 查看正在使用的数据库
create database if not exists $databaseName engine=$engineName character set $charSet;
alter database $database $command;	# 修改数据库信息
drop database if exists $databaseName;
use $database;
show status status;					# 查看库中表的属性信息
```

> 注意：数据库一旦创建无法修改名称

#### 数据库备份/恢复

```shell
mysqldump -u $username (-d) $database > $file -p $password	# 备份（-d：仅备份结构）
mysqldump -u $username $database < $file -p $password		# 恢复
```

### 表

```mysql
show tables (as $database);
describe $table;							# 查看表的字段结构
create table $tableNme($columnName $dataType $constraint);
alter table $table 
	add $column [first/after $columnName];	# 添加字段
	modify $column $type;					# 修改字段数据类型
	change $column $columnName $type;		# 修改字段属性
	drop column $column;					# 删除字段 
	rename to $tablename					# 重命名表
delete from $tableName;						# 清空表
rename table $table to $tableName;			# 重命名表
truncate table $tableName;					# 重置自增，快速清空表（不支持rollback）
drop table $tableName;						# 删除表
```

### 记录

#### 增

```mysql
insert into $tableName($columnNames) values($values);	# 逐行插入
insert into $tableName($columnNames) select $columnNames from $sourceTable;	# 导入已有数据
```

#### 删

```mysql
delete from $tableName where $条件;
```

#### 改

```mysql
update $tableName set $key=$value where $条件;
replace $tableName set $key=$value where $条件;	# 增/改（慎用）
```

#### 查

```mysql
select 
	distinct						# 对查询结果去重
	function()
	$column 
	as $newColumnName 
	from $table 
	group by $columnName
	having $条件						# 对聚合结果二次筛选，且having须位于group by之后
	where $条件 
	order by $columnName desc/asc;			
# Default,show them on screen
```

**where后面能用啥**：

> - \>,<,=,!=,\<>（<>相当于!=）
>
> - and,or,not
>
> - in ($enum)
>
> - between and（闭区间）
>
> - select套娃
>
> - **like**：
>
>   > - "%"：任意多个字符
>   >
>   > - "_"：一个字符
>
> - **limit**：
>
>   > - a：前a条
>   >
>   > - a,b：从a开始的b条
>   >
>   > - b offset a：同a,b
>   >

### 连表

> 将多张表以一定的关系拼接起来


```mysql
inner/full/nature/ /left/right(outer) join /union $tableB on $tableA.$columnA = $tableB.$columnB /using ($column);
```

> - **inner**：内连接，取交集，采用先求笛卡尔积后筛选的方式操作
>
> - **left**：左连接，以左表为准
>
> - **right**：右连接，以右表为准
>
> - **full**：完全连接，取并集（MySQL不支持）
>
> - **cross**：交叉连接，后无on语句（甚至可以不写cross join语句），与无on语句的内连接相同
>
> - **nature**：自然连接，自动查找表中相同字段并连接
>
> - **union**：将两组数据合并并去重（使用union all取消去重）
>
>   考虑到效率，尽量使用union all
>
> - **using**：简化on语句

### 约束

> 为了保证数据库的**数据完整性**，有时需要额外添加一些对表或字段的**约束**（constraint）。

&emsp;&emsp;既可以在创建表时添加约束，又可以在创建表后添加、修改、删除约束。

```mysql
alter table $table modify $column $constraints;								# 添加列约束
alter table $table add constraint $constraintName $constraint($columns);	# 添加表约束
```

&emsp;&emsp;约束种类包括：

- **primary key**：主键，要求值唯一且不为空，表内只有一个主键约束

- **check**：检查约束，检查值是否满足预先定义的规则（MySQL5.7 不支持）

- **auto_increment**：自增，要求被设置为primary key且唯一

  > auto_increament：下一个记录中的值

  ```mysql
  show session variables like "auto_increament%";		# 查看会话变量
  set session auto_increament_increament = $value;	# 设置自增步长(会话)
  set global auto_increament_increament = $value;		# 设置自增步长（全局）
  set session auto_increament_offset = $value;		# 设置自增起始值(会话)
  set global auto_increament_offset = $value;			# 设置自增起始值（全局）
  ```

- **unique**：值唯一（可为空且NULL不断重复），可加速查找

- **default**：设置默认值

- **not null**：非空

- **geberated**：生成列，根据表达式确定数据值，并确定是否储存在表中。

  ```mysql
  generated always as $exp virtual/stored
  ```

- **foreign key**：外键，将某一列的值限制在一个可变范围内，相当于升级版enum（n对多）

  ```mysql
  constraint $constraintName foreign key ($columnNames) references $tableName($primaryKeyName)
  ```

  >外键不能跨引擎使用。

### 临时表（子查询）

```mysql
($data) as $yempTable
```

临时表的作用域为当前语句

### 范式

&emsp;&emsp;在创建关系型数据库的数据表时，为了规范表的结构，形成了以下三条规定：

> - 所有字段不可拆分
> - 必须存在业务主键，且非主键字段依赖主键字段
> - 非主键字段不能依赖除主键字段外的其他字段（避免数据冗余）

&emsp;&emsp;当然，有时考录到实际需求的特殊性，会采用反范式的设计。

## 数据类型

### 数字

|     类型     |                   大小                   |                        范围（有符号）                        |                        范围（无符号）                        |      用途       |
| :----------: | :--------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: | :-------------: |
|   TINYINT    |                 1 Bytes                  |                         (-128，127)                          |                           (0，255)                           |    小整数值     |
|   SMALLINT   |                 2 Bytes                  |                      (-32 768，32 767)                       |                         (0，65 535)                          |    大整数值     |
|  MEDIUMINT   |                 3 Bytes                  |                   (-8 388 608，8 388 607)                    |                       (0，16 777 215)                        |    大整数值     |
| INT或INTEGER |                 4 Bytes                  |               (-2 147 483 648，2 147 483 647)                |                      (0，4 294 967 295)                      |    大整数值     |
|    BIGINT    |                 8 Bytes                  |   (-9,223,372,036,854,775,808，9 223 372 036 854 775 807)    |               (0，18 446 744 073 709 551 615)                |   极大整数值    |
|    FLOAT     |                 4 Bytes                  | (-3.402 823 466 E+38，-1.175 494 351 E-38)，0，(1.175 494 351 E-38，3.402 823 466 351 E+38) |         0，(1.175 494 351 E-38，3.402 823 466 E+38)          | 单精度 浮点数值 |
|    DOUBLE    |                 8 Bytes                  | (-1.797 693 134 862 315 7 E+308，-2.225 073 858 507 201 4 E-308)，0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 双精度 浮点数值 |
|   DECIMAL    | 对DECIMAL(M,D) ，如果M>D，为M+2否则为D+2 |                        依赖于M和D的值                        |                        依赖于M和D的值                        |    定点小数     |

### 日期时间

|   类型    | 大小 ( bytes) |                             范围                             |        格式         |           用途           |
| :-------: | :-----------: | :----------------------------------------------------------: | :-----------------: | :----------------------: |
|   DATE    |       3       |                    1000-01-01/9999-12-31                     |     YYYY-MM-DD      |          日期值          |
|   TIME    |       3       |                   '-838:59:59'/'838:59:59'                   |      HH:MM:SS       |     时间值或持续时间     |
|   YEAR    |       1       |                          1901/2155                           |        YYYY         |          年份值          |
| DATETIME  |       8       |           1000-01-01 00:00:00/9999-12-31 23:59:59            | YYYY-MM-DD HH:MM:SS |     混合日期和时间值     |
| TIMESTAMP |       4       | 1970-01-01 00:00:00/2038结束时间是第 **2147483647** 秒，北京时间 **2038-1-19 11:14:07**，格林尼治时间 2038年1月19日 凌晨 03:14:07 |   YYYYMMDD HHMMSS   | 混合日期和时间值，时间戳 |

### 字符

|    类型    |         大小          |              用途               |
| :--------: | :-------------------: | :-----------------------------: |
|    CHAR    |      0-255 bytes      |           定长字符串            |
|  VARCHAR   |     0-65535 bytes     |           变长字符串            |
|  TINYBLOB  |      0-255 bytes      | 不超过 255 个字符的二进制字符串 |
|  TINYTEXT  |      0-255 bytes      |          短文本字符串           |
|    BLOB    |    0-65 535 bytes     |     二进制形式的长文本数据      |
|    TEXT    |    0-65 535 bytes     |           长文本数据            |
| MEDIUMBLOB |  0-16 777 215 bytes   |  二进制形式的中等长度文本数据   |
| MEDIUMTEXT |  0-16 777 215 bytes   |        中等长度文本数据         |
|  LONGBLOB  | 0-4 294 967 295 bytes |    二进制形式的极大文本数据     |
|  LONGTEXT  | 0-4 294 967 295 bytes |          极大文本数据           |

### 空间

|        类型        |           用途            |
| :----------------: | :-----------------------: |
|      GEOMETRY      |     任何类型的空间值      |
|       POINT        |    一个点(一对X-Y坐标)    |
|     LINESTRING     | 曲线(一个或多个`POINT`值) |
|      POLYGON       |          多边形           |
| GEOMETRYCOLLECTION |    `GEOMETRY`值的集合     |
|  MULTILINESTRING   |   `LINESTRING`值的集合    |
|     MULTIPOINT     |      `POINT`值的集合      |
|    MULTIPOLYGON    |     `POLYGON`值的集合     |

### 特殊

- enum：枚举
- set：n选m

> 为了加速查找，将定长数据放在前面，变长数据放在后面

### 数据类型的选择

- 允许就用unsignd
- 自增id用bi（bigint）
- 身高体重int来
- 整数括号表补齐
- 定长储存小数据
- varchar放到最后放
- 浮点小数有误差
- dc（decimal）左总右小数
- datetime有点大
- ts（timestamp）快要过期了

## 触发器

> 触发器是在执行某些MySQL语句前后自动执行特定语句的一种工具。

### 创建触发器

```mysql
DELIMITER $str									# 为了输入多行SQL语句，暂时修改语句截止符
CREATE TRIGGER $triggerName
	BEFORE/AFTER INSERT/DELETE/UPDATE ON $tablename FOR EACH ROW
BEGIN
	$SQLs...
END
DELIMITER ;
# 可用NEW、OLD引用主动变化的数据
```

### 删除触发器

```mysql
DROP TRIGGER $triggerName;
```

## 视图

&emsp;&emsp;视图是对某查询语句的复用，其数据依赖于真实的表，具有只读、高效、安全的特点。

```mysql
create 
(algorithm = undefined/murge/temptable)	# 算法
view $viewName as $sql;							# 创建视图
alter view $viewName as $sql;					# 修改视图
drop view $viewname								# 删除视图
```

## 函数

### 内置函数

> 参照MySQL官方文档：[MySQL5.7 Functions and Operators](https://dev.mysql.com/doc/refman/5.7/en/functions.html)
>

#### 字符函数

|                 函数                  | 描述                                                         | 实例                                                         |
| :-----------------------------------: | :----------------------------------------------------------- | :----------------------------------------------------------- |
|               ASCII(s)                | 返回字符串 s 的第一个字符的 ASCII 码。                       | 返回 CustomerName 字段第一个字母的 ASCII 码：`SELECT ASCII(CustomerName) AS NumCodeOfFirstChar FROM Customers;` |
|            CHAR_LENGTH(s)             | 返回字符串 s 的字符数                                        | 返回字符串 RUNOOB 的字符数`SELECT CHAR_LENGTH("RUNOOB") AS LengthOfString;` |
|          CHARACTER_LENGTH(s)          | 返回字符串 s 的字符数                                        | 返回字符串 RUNOOB 的字符数`SELECT CHARACTER_LENGTH("RUNOOB") AS LengthOfString;` |
|          CONCAT(s1,s2...sn)           | 字符串 s1,s2 等多个字符串合并为一个字符串                    | 合并多个字符串`SELECT CONCAT("SQL ", "Runoob ", "Gooogle ", "Facebook") AS ConcatenatedString;` |
|       CONCAT_WS(x, s1,s2...sn)        | 同 CONCAT(s1,s2,...) 函数，但是每个字符串之间要加上 x，x 可以是分隔符 | 合并多个字符串，并添加分隔符：`SELECT CONCAT_WS("-", "SQL", "Tutorial", "is", "fun!")AS ConcatenatedString;` |
|           FIELD(s,s1,s2...)           | 返回第一个字符串 s 在字符串列表(s1,s2...)中的位置            | 返回字符串 c 在列表值中的位置：`SELECT FIELD("c", "a", "b", "c", "d", "e");` |
|          FIND_IN_SET(s1,s2)           | 返回在字符串s2中与s1匹配的字符串的位置                       | 返回字符串 c 在指定字符串中的位置：`SELECT FIND_IN_SET("c", "a,b,c,d,e");` |
|              FORMAT(x,n)              | 函数可以将数字 x 进行格式化 "#,###.##", 将 x 保留到小数点后 n 位，最后一位四舍五入。 | 格式化数字 "#,###.##" 形式：`SELECT FORMAT(250500.5634, 2);     -- 输出 250,500.56` |
|          INSERT(s1,x,len,s2)          | 字符串 s2 替换 s1 的 x 位置开始长度为 len 的字符串           | 从字符串第一个位置开始的 6 个字符替换为 runoob：`SELECT INSERT("google.com", 1, 6, "runoob");  -- 输出：runoob.com` |
|             LOCATE(s1,s)              | 从字符串 s 中获取 s1 的开始位置                              | 获取 b 在字符串 abc 中的位置：`SELECT LOCATE('st','myteststring');  -- 5`返回字符串 abc 中 b 的位置：`SELECT LOCATE('b', 'abc') -- 2` |
|               LCASE(s)                | 将字符串 s 的所有字母变成小写字母                            | 字符串 RUNOOB 转换为小写：`SELECT LCASE('RUNOOB') -- runoob` |
|               LEFT(s,n)               | 返回字符串 s 的前 n 个字符                                   | 返回字符串 runoob 中的前两个字符：`SELECT LEFT('runoob',2) -- ru` |
|               LOWER(s)                | 将字符串 s 的所有字母变成小写字母                            | 字符串 RUNOOB 转换为小写：`SELECT LOWER('RUNOOB') -- runoob` |
|            LPAD(s1,len,s2)            | 在字符串 s1 的开始处填充字符串 s2，使字符串长度达到 len      | 将字符串 xx 填充到 abc 字符串的开始处：`SELECT LPAD('abc',5,'xx') -- xxabc` |
|               LTRIM(s)                | 去掉字符串 s 开始处的空格                                    | 去掉字符串 RUNOOB开始处的空格：`SELECT LTRIM("    RUNOOB") AS LeftTrimmedString;-- RUNOOB` |
|             MID(s,n,len)              | 从字符串 s 的 n 位置截取长度为 len 的子字符串，同 SUBSTRING(s,n,len) | 从字符串 RUNOOB 中的第 2 个位置截取 3个 字符：`SELECT MID("RUNOOB", 2, 3) AS ExtractString; -- UNO` |
|           POSITION(s1 IN s)           | 从字符串 s 中获取 s1 的开始位置                              | 返回字符串 abc 中 b 的位置：`SELECT POSITION('b' in 'abc') -- 2` |
|              REPEAT(s,n)              | 将字符串 s 重复 n 次                                         | 将字符串 runoob 重复三次：`SELECT REPEAT('runoob',3) -- runoobrunoobrunoob` |
|           REPLACE(s,s1,s2)            | 将字符串 s2 替代字符串 s 中的字符串 s1                       | 将字符串 abc 中的字符 a 替换为字符 x：`SELECT REPLACE('abc','a','x') --xbc` |
|              REVERSE(s)               | 将字符串s的顺序反过来                                        | 将字符串 abc 的顺序反过来：`SELECT REVERSE('abc') -- cba`    |
|              RIGHT(s,n)               | 返回字符串 s 的后 n 个字符                                   | 返回字符串 runoob 的后两个字符：`SELECT RIGHT('runoob',2) -- ob` |
|            RPAD(s1,len,s2)            | 在字符串 s1 的结尾处添加字符串 s2，使字符串的长度达到 len    | 将字符串 xx 填充到 abc 字符串的结尾处：`SELECT RPAD('abc',5,'xx') -- abcxx` |
|               RTRIM(s)                | 去掉字符串 s 结尾处的空格                                    | 去掉字符串 RUNOOB 的末尾空格：`SELECT RTRIM("RUNOOB     ") AS RightTrimmedString;   -- RUNOOB` |
|               SPACE(n)                | 返回 n 个空格                                                | 返回 10 个空格：`SELECT SPACE(10);`                          |
|             STRCMP(s1,s2)             | 比较字符串 s1 和 s2，如果 s1 与 s2 相等返回 0 ，如果 s1>s2 返回 1，如果 s1<s2 返回 -1 | 比较字符串：`SELECT STRCMP("runoob", "runoob");  -- 0`       |
|       SUBSTR(s, start, length)        | 从字符串 s 的 start 位置截取长度为 length 的子字符串         | 从字符串 RUNOOB 中的第 2 个位置截取 3个 字符：`SELECT SUBSTR("RUNOOB", 2, 3) AS ExtractString; -- UNO` |
|      SUBSTRING(s, start, length)      | 从字符串 s 的 start 位置截取长度为 length 的子字符串         | 从字符串 RUNOOB 中的第 2 个位置截取 3个 字符：`SELECT SUBSTRING("RUNOOB", 2, 3) AS ExtractString; -- UNO` |
| SUBSTRING_INDEX(s, delimiter, number) | 返回从字符串 s 的第 number 个出现的分隔符 delimiter 之后的子串。 如果 number 是正数，返回第 number 个字符左边的字符串。 如果 number 是负数，返回第(number 的绝对值(从右边数))个字符右边的字符串。 | `SELECT SUBSTRING_INDEX('a*b','*',1) -- a SELECT SUBSTRING_INDEX('a*b','*',-1)  -- b SELECT SUBSTRING_INDEX(SUBSTRING_INDEX('a*b*c*d*e','*',3),'*',-1)  -- c` |
|                TRIM(s)                | 去掉字符串 s 开始和结尾处的空格                              | 去掉字符串 RUNOOB 的首尾空格：`SELECT TRIM('    RUNOOB    ') AS TrimmedString;` |
|               UCASE(s)                | 将字符串转换为大写                                           | 将字符串 runoob 转换为大写：`SELECT UCASE("runoob"); -- RUNOOB` |
|               UPPER(s)                | 将字符串转换为大写                                           | 将字符串 runoob 转换为大写：`SELECT UPPER("runoob"); -- RUNOOB` |

#### 数字函数

|               函数名               | 描述                                                         | 实例                                                         |
| :--------------------------------: | :----------------------------------------------------------- | :----------------------------------------------------------- |
|               ABS(x)               | 返回 x 的绝对值                                              | 返回 -1 的绝对值：`SELECT ABS(-1) -- 返回1`                  |
|              ACOS(x)               | 求 x 的反余弦值（单位为弧度），x 为一个数值                  | `SELECT ACOS(0.25);`                                         |
|              ASIN(x)               | 求反正弦值（单位为弧度），x 为一个数值                       | `SELECT ASIN(0.25);`                                         |
|              ATAN(x)               | 求反正切值（单位为弧度），x 为一个数值                       | `SELECT ATAN(2.5);`                                          |
|            ATAN2(n, m)             | 求反正切值（单位为弧度）                                     | `SELECT ATAN2(-0.8, 2);`                                     |
|          AVG(expression)           | 返回一个表达式的平均值，expression 是一个字段                | 返回 Products 表中Price 字段的平均值：`SELECT AVG(Price) AS AveragePrice FROM Products;` |
|              CEIL(x)               | 返回大于或等于 x 的最小整数                                  | `SELECT CEIL(1.5) -- 返回2`                                  |
|             CEILING(x)             | 返回大于或等于 x 的最小整数                                  | `SELECT CEILING(1.5); -- 返回2`                              |
|               COS(x)               | 求余弦值(参数是弧度)                                         | `SELECT COS(2);`                                             |
|               COT(x)               | 求余切值(参数是弧度)                                         | `SELECT COT(6);`                                             |
|         COUNT(expression)          | 返回查询的记录总数，expression 参数是一个字段或者 * 号       | 返回 Products 表中 products 字段总共有多少条记录：`SELECT COUNT(ProductID) AS NumberOfProducts FROM Products;` |
|             DEGREES(x)             | 将弧度转换为角度                                             | `SELECT DEGREES(3.1415926535898) -- 180`                     |
|              n DIV m               | 整除，n 为被除数，m 为除数                                   | 计算 10 除于 5：`SELECT 10 DIV 5;  -- 2`                     |
|               EXP(x)               | 返回 e 的 x 次方                                             | 计算 e 的三次方：`SELECT EXP(3) -- 20.085536923188`          |
|              FLOOR(x)              | 返回小于或等于 x 的最大整数                                  | 小于或等于 1.5 的整数：`SELECT FLOOR(1.5) -- 返回1`          |
| GREATEST(expr1, expr2, expr3, ...) | 返回列表中的最大值                                           | 返回以下数字列表中的最大值：`SELECT GREATEST(3, 12, 34, 8, 25); -- 34`返回以下字符串列表中的最大值：`SELECT GREATEST("Google", "Runoob", "Apple");   -- Runoob` |
|  LEAST(expr1, expr2, expr3, ...)   | 返回列表中的最小值                                           | 返回以下数字列表中的最小值：`SELECT LEAST(3, 12, 34, 8, 25); -- 3`返回以下字符串列表中的最小值：`SELECT LEAST("Google", "Runoob", "Apple");   -- Apple` |
|                 LN                 | 返回数字的自然对数，以 e 为底。                              | 返回 2 的自然对数：`SELECT LN(2);  -- 0.6931471805599453`    |
|       LOG(x) 或 LOG(base, x)       | 返回自然对数(以 e 为底的对数)，如果带有 base 参数，则 base 为指定带底数。 | `SELECT LOG(20.085536923188) -- 3 SELECT LOG(2, 4); -- 2`    |
|              LOG10(x)              | 返回以 10 为底的对数                                         | `SELECT LOG10(100) -- 2`                                     |
|              LOG2(x)               | 返回以 2 为底的对数                                          | 返回以 2 为底 6 的对数：`SELECT LOG2(6);  -- 2.584962500721156` |
|          MAX(expression)           | 返回字段 expression 中的最大值                               | 返回数据表 Products 中字段 Price 的最大值：`SELECT MAX(Price) AS LargestPrice FROM Products;` |
|          MIN(expression)           | 返回字段 expression 中的最小值                               | 返回数据表 Products 中字段 Price 的最小值：`SELECT MIN(Price) AS MinPrice FROM Products;` |
|              MOD(x,y)              | 返回 x 除以 y 以后的余数                                     | 5 除于 2 的余数：`SELECT MOD(5,2) -- 1`                      |
|                PI()                | 返回圆周率(3.141593）                                        | `SELECT PI() --3.141593`                                     |
|              POW(x,y)              | 返回 x 的 y 次方                                             | 2 的 3 次方：`SELECT POW(2,3) -- 8`                          |
|             POWER(x,y)             | 返回 x 的 y 次方                                             | 2 的 3 次方：`SELECT POWER(2,3) -- 8`                        |
|             RADIANS(x)             | 将角度转换为弧度                                             | 180 度转换为弧度：`SELECT RADIANS(180) -- 3.1415926535898`   |
|               RAND()               | 返回 0 到 1 的随机数                                         | `SELECT RAND() --0.93099315644334`                           |
|              ROUND(x)              | 返回离 x 最近的整数                                          | `SELECT ROUND(1.23456) --1`                                  |
|              SIGN(x)               | 返回 x 的符号，x 是负数、0、正数分别返回 -1、0 和 1          | `SELECT SIGN(-10) -- (-1)`                                   |
|               SIN(x)               | 求正弦值(参数是弧度)                                         | `SELECT SIN(RADIANS(30)) -- 0.5`                             |
|              SQRT(x)               | 返回x的平方根                                                | 25 的平方根：`SELECT SQRT(25) -- 5`                          |
|          SUM(expression)           | 返回指定字段的总和                                           | 计算 OrderDetails 表中字段 Quantity 的总和：`SELECT SUM(Quantity) AS TotalItemsOrdered FROM OrderDetails;` |
|               TAN(x)               | 求正切值(参数是弧度)                                         | `SELECT TAN(1.75);  -- -5.52037992250933`                    |
|           TRUNCATE(x,y)            | 返回数值 x 保留到小数点后 y 位的值（与 ROUND 最大的区别是不会进行四舍五入） | `SELECT TRUNCATE(1.23456,3) -- 1.234`                        |

#### 日期时间函数

|                      函数名                       | 描述                                                         | 实例                                                         |
| :-----------------------------------------------: | :----------------------------------------------------------- | :----------------------------------------------------------- |
|                   ADDDATE(d,n)                    | 计算起始日期 d 加上 n 天的日期                               | `SELECT ADDDATE("2017-06-15", INTERVAL 10 DAY); ->2017-06-25` |
|                   ADDTIME(t,n)                    | n 是一个时间表达式，时间 t 加上时间表达式 n                  | 加 5 秒：`SELECT ADDTIME('2011-11-11 11:11:11', 5); ->2011-11-11 11:11:16 (秒)`添加 2 小时, 10 分钟, 5 秒:`SELECT ADDTIME("2020-06-15 09:34:21", "2:10:5");  -> 2020-06-15 11:44:26` |
|                     CURDATE()                     | 返回当前日期                                                 | `SELECT CURDATE(); -> 2018-09-19`                            |
|                  CURRENT_DATE()                   | 返回当前日期                                                 | `SELECT CURRENT_DATE(); -> 2018-09-19`                       |
|                   CURRENT_TIME                    | 返回当前时间                                                 | `SELECT CURRENT_TIME(); -> 19:59:02`                         |
|                CURRENT_TIMESTAMP()                | 返回当前日期和时间                                           | `SELECT CURRENT_TIMESTAMP() -> 2018-09-19 20:57:43`          |
|                     CURTIME()                     | 返回当前时间                                                 | `SELECT CURTIME(); -> 19:59:02`                              |
|                      DATE()                       | 从日期或日期时间表达式中提取日期值                           | `SELECT DATE("2017-06-15");     -> 2017-06-15`               |
|                  DATEDIFF(d1,d2)                  | 计算日期 d1->d2 之间相隔的天数                               | `SELECT DATEDIFF('2001-01-01','2001-02-02') -> -32`          |
|          DATE_ADD(d，INTERVAL expr type)          | 计算起始日期 d 加上一个时间段后的日期，type 值可以是：MICROSECOND, SECOND, MINUTE, HOUR, DAY, WEEK, MONTH, QUARTER, YEAR, SECOND_MICROSECOND, MINUTE_MICROSECOND, MINUTE_SECOND, HOUR_MICROSECOND, HOUR_SECOND, HOUR_MINUTE, DAY_MICROSECOND, DAY_SECOND, DAY_MINUTE, DAY_HOUR, YEAR_MONTH | `SELECT DATE_ADD("2017-06-15", INTERVAL 10 DAY);     -> 2017-06-25 SELECT DATE_ADD("2017-06-15 09:34:21", INTERVAL 15 MINUTE); -> 2017-06-15 09:49:21 SELECT DATE_ADD("2017-06-15 09:34:21", INTERVAL -3 HOUR); ->2017-06-15 06:34:21 SELECT DATE_ADD("2017-06-15 09:34:21", INTERVAL -3 MONTH); ->2017-04-15` |
|                 DATE_FORMAT(d,f)                  | 按表达式 f的要求显示日期 d                                   | `SELECT DATE_FORMAT('2011-11-11 11:11:11','%Y-%m-%d %r') -> 2011-11-11 11:11:11 AM` |
|         DATE_SUB(date,INTERVAL expr type)         | 函数从日期减去指定的时间间隔。                               | Orders 表中 OrderDate 字段减去 2 天：`SELECT OrderId,DATE_SUB(OrderDate,INTERVAL 2 DAY) AS OrderPayDate FROM Orders` |
|                      DAY(d)                       | 返回日期值 d 的日期部分                                      | `SELECT DAY("2017-06-15");   -> 15`                          |
|                    DAYNAME(d)                     | 返回日期 d 是星期几，如 Monday,Tuesday                       | `SELECT DAYNAME('2011-11-11 11:11:11') ->Friday`             |
|                   DAYOFMONTH(d)                   | 计算日期 d 是本月的第几天                                    | `SELECT DAYOFMONTH('2011-11-11 11:11:11') ->11`              |
|                   DAYOFWEEK(d)                    | 日期 d 今天是星期几，1 星期日，2 星期一，以此类推            | `SELECT DAYOFWEEK('2011-11-11 11:11:11') ->6`                |
|                   DAYOFYEAR(d)                    | 计算日期 d 是本年的第几天                                    | `SELECT DAYOFYEAR('2011-11-11 11:11:11') ->315`              |
|               EXTRACT(type FROM d)                | 从日期 d 中获取指定的值，type 指定返回的值。 type可取值为： MICROSECOND, SECOND, MINUTE, HOUR, DAY, WEEK, MONTH, QUARTER, YEAR, SECOND_MICROSECOND, MINUTE_MICROSECOND, MINUTE_SECOND, HOUR_MICROSECOND, HOUR_SECOND, HOUR_MINUTE, DAY_MICROSECOND, DAY_SECOND, DAY_MINUTE, DAY_HOUR, YEAR_MONTH | `SELECT EXTRACT(MINUTE FROM '2011-11-11 11:11:11')  -> 11`   |
|                   FROM_DAYS(n)                    | 计算从 0000 年 1 月 1 日开始 n 天后的日期                    | `SELECT FROM_DAYS(1111) -> 0003-01-16`                       |
|                      HOUR(t)                      | 返回 t 中的小时值                                            | `SELECT HOUR('1:2:3') -> 1`                                  |
|                    LAST_DAY(d)                    | 返回给给定日期的那一月份的最后一天                           | `SELECT LAST_DAY("2017-06-20"); -> 2017-06-30`               |
|                    LOCALTIME()                    | 返回当前日期和时间                                           | `SELECT LOCALTIME() -> 2018-09-19 20:57:43`                  |
|                 LOCALTIMESTAMP()                  | 返回当前日期和时间                                           | `SELECT LOCALTIMESTAMP() -> 2018-09-19 20:57:43`             |
|            MAKEDATE(year, day-of-year)            | 基于给定参数年份 year 和所在年中的天数序号 day-of-year 返回一个日期 | `SELECT MAKEDATE(2017, 3); -> 2017-01-03`                    |
|          MAKETIME(hour, minute, second)           | 组合时间，参数分别为小时、分钟、秒                           | `SELECT MAKETIME(11, 35, 4); -> 11:35:04`                    |
|                 MICROSECOND(date)                 | 返回日期参数所对应的微秒数                                   | `SELECT MICROSECOND("2017-06-20 09:34:00.000023"); -> 23`    |
|                     MINUTE(t)                     | 返回 t 中的分钟值                                            | `SELECT MINUTE('1:2:3') -> 2`                                |
|                   MONTHNAME(d)                    | 返回日期当中的月份名称，如 November                          | `SELECT MONTHNAME('2011-11-11 11:11:11') -> November`        |
|                     MONTH(d)                      | 返回日期d中的月份值，1 到 12                                 | `SELECT MONTH('2011-11-11 11:11:11') ->11`                   |
|                       NOW()                       | 返回当前日期和时间                                           | `SELECT NOW() -> 2018-09-19 20:57:43`                        |
|            PERIOD_ADD(period, number)             | 为 年-月 组合日期添加一个时段                                | `SELECT PERIOD_ADD(201703, 5);    -> 201708`                 |
|           PERIOD_DIFF(period1, period2)           | 返回两个时段之间的月份差值                                   | `SELECT PERIOD_DIFF(201710, 201703); -> 7`                   |
|                    QUARTER(d)                     | 返回日期d是第几季节，返回 1 到 4                             | `SELECT QUARTER('2011-11-11 11:11:11') -> 4`                 |
|                     SECOND(t)                     | 返回 t 中的秒钟值                                            | `SELECT SECOND('1:2:3') -> 3`                                |
|                  SEC_TO_TIME(s)                   | 将以秒为单位的时间 s 转换为时分秒的格式                      | `SELECT SEC_TO_TIME(4320) -> 01:12:00`                       |
|         STR_TO_DATE(string, format_mask)          | 将字符串转变为日期                                           | `SELECT STR_TO_DATE("August 10 2017", "%M %d %Y"); -> 2017-08-10` |
|                   SUBDATE(d,n)                    | 日期 d 减去 n 天后的日期                                     | `SELECT SUBDATE('2011-11-11 11:11:11', 1) ->2011-11-10 11:11:11 (默认是天)` |
|                   SUBTIME(t,n)                    | 时间 t 减去 n 秒的时间                                       | `SELECT SUBTIME('2011-11-11 11:11:11', 5) ->2011-11-11 11:11:06 (秒)` |
|                     SYSDATE()                     | 返回当前日期和时间                                           | `SELECT SYSDATE() -> 2018-09-19 20:57:43`                    |
|                 TIME(expression)                  | 提取传入表达式的时间部分                                     | `SELECT TIME("19:30:10"); -> 19:30:10`                       |
|                 TIME_FORMAT(t,f)                  | 按表达式 f 的要求显示时间 t                                  | `SELECT TIME_FORMAT('11:11:11','%r') 11:11:11 AM`            |
|                  TIME_TO_SEC(t)                   | 将时间 t 转换为秒                                            | `SELECT TIME_TO_SEC('1:12:00') -> 4320`                      |
|              TIMEDIFF(time1, time2)               | 计算时间差值                                                 | `mysql> SELECT TIMEDIFF("13:10:11", "13:10:10"); -> 00:00:01 mysql> SELECT TIMEDIFF('2000:01:01 00:00:00',    ->                 '2000:01:01 00:00:00.000001');        -> '-00:00:00.000001' mysql> SELECT TIMEDIFF('2008-12-31 23:59:59.000001',    ->                 '2008-12-30 01:01:01.000002');        -> '46:58:57.999999'` |
|          TIMESTAMP(expression, interval)          | 单个参数时，函数返回日期或日期时间表达式；有2个参数时，将参数加和 | `mysql> SELECT TIMESTAMP("2017-07-23",  "13:10:11"); -> 2017-07-23 13:10:11 mysql> SELECT TIMESTAMP('2003-12-31');        -> '2003-12-31 00:00:00' mysql> SELECT TIMESTAMP('2003-12-31 12:00:00','12:00:00');        -> '2004-01-01 00:00:00'` |
| TIMESTAMPDIFF(unit,datetime_expr1,datetime_expr2) | 计算时间差，返回 datetime_expr2 − datetime_expr1 的时间差    | `mysql> SELECT TIMESTAMPDIFF(DAY,'2003-02-01','2003-05-01');   // 计算两个时间相隔多少天        -> 89 mysql> SELECT TIMESTAMPDIFF(MONTH,'2003-02-01','2003-05-01');   // 计算两个时间相隔多少月        -> 3 mysql> SELECT TIMESTAMPDIFF(YEAR,'2002-05-01','2001-01-01');    // 计算两个时间相隔多少年        -> -1 mysql> SELECT TIMESTAMPDIFF(MINUTE,'2003-02-01','2003-05-01 12:05:55');  // 计算两个时间相隔多少分钟        -> 128885` |
|                    TO_DAYS(d)                     | 计算日期 d 距离 0000 年 1 月 1 日的天数                      | `SELECT TO_DAYS('0001-01-01 01:01:01') -> 366`               |
|                      WEEK(d)                      | 计算日期 d 是本年的第几个星期，范围是 0 到 53                | `SELECT WEEK('2011-11-11 11:11:11') -> 45`                   |
|                    WEEKDAY(d)                     | 日期 d 是星期几，0 表示星期一，1 表示星期二                  | `SELECT WEEKDAY("2017-06-15"); -> 3`                         |
|                   WEEKOFYEAR(d)                   | 计算日期 d 是本年的第几个星期，范围是 0 到 53                | `SELECT WEEKOFYEAR('2011-11-11 11:11:11') -> 45`             |
|                      YEAR(d)                      | 返回年份                                                     | `SELECT YEAR("2017-06-15"); -> 2017`                         |
|               YEARWEEK(date, mode)                | 返回年份及第几周（0到53），mode 中 0 表示周天，1表示周一，以此类推 | `SELECT YEARWEEK("2017-06-15"); -> 201724`                   |

#### 高级函数

|                            函数名                            | 描述                                                         | 实例                                                         |
| :----------------------------------------------------------: | :----------------------------------------------------------- | :----------------------------------------------------------- |
|                            BIN(x)                            | 返回 x 的二进制编码                                          | 15 的 2 进制编码:`SELECT BIN(15); -- 1111`                   |
|                          BINARY(s)                           | 将字符串 s 转换为二进制字符串                                | `SELECT BINARY "RUNOOB"; -> RUNOOB`                          |
| `CASE expression    WHEN condition1 THEN result1    WHEN condition2 THEN result2   ...    WHEN conditionN THEN resultN    ELSE result END` | CASE 表示函数开始，END 表示函数结束。如果 condition1 成立，则返回 result1, 如果 condition2 成立，则返回 result2，当全部不成立则返回 result，而当有一个成立之后，后面的就不执行了。 | `SELECT CASE  　WHEN 1 > 0 　THEN '1 > 0' 　WHEN 2 > 0 　THEN '2 > 0' 　ELSE '3 > 0' 　END ->1 > 0` |
|                       CAST(x AS type)                        | 转换数据类型                                                 | 字符串日期转换为日期：`SELECT CAST("2017-08-29" AS DATE); -> 2017-08-29` |
|             COALESCE(expr1, expr2, ...., expr_n)             | 返回参数中的第一个非空表达式（从左向右）                     | `SELECT COALESCE(NULL, NULL, NULL, 'runoob.com', NULL, 'google.com'); -> runoob.com` |
|                       CONNECTION_ID()                        | 返回唯一的连接 ID                                            | `SELECT CONNECTION_ID(); -> 4292835`                         |
|                        CONV(x,f1,f2)                         | 返回 f1 进制数变成 f2 进制数                                 | `SELECT CONV(15, 10, 2); -> 1111`                            |
|                     CONVERT(s USING cs)                      | 函数将字符串 s 的字符集变成 cs                               | `SELECT CHARSET('ABC') ->utf-8     SELECT CHARSET(CONVERT('ABC' USING gbk)) ->gbk` |
|                        CURRENT_USER()                        | 返回当前用户                                                 | `SELECT CURRENT_USER(); -> guest@%`                          |
|                          DATABASE()                          | 返回当前数据库名                                             | `SELECT DATABASE();    -> runoob`                            |
|                        IF(expr,v1,v2)                        | 如果表达式 expr 成立，返回结果 v1；否则，返回结果 v2。       | `SELECT IF(1 > 0,'正确','错误')     ->正确`                  |
| [IFNULL(v1,v2)](https://www.runoob.com/mysql/mysql-func-ifnull.html) | 如果 v1 的值不为 NULL，则返回 v1，否则返回 v2。              | `SELECT IFNULL(null,'Hello Word') ->Hello Word`              |
|                      ISNULL(expression)                      | 判断表达式是否为 NULL                                        | `SELECT ISNULL(NULL); ->1`                                   |
|                       LAST_INSERT_ID()                       | 返回最近生成的 AUTO_INCREMENT 值                             | `SELECT LAST_INSERT_ID(); ->6`                               |
|                     NULLIF(expr1, expr2)                     | 比较两个字符串，如果字符串 expr1 与 expr2 相等 返回 NULL，否则返回 expr1 | `SELECT NULLIF(25, 25); ->`                                  |
|                        SESSION_USER()                        | 返回当前用户                                                 | `SELECT SESSION_USER(); -> guest@%`                          |
|                        SYSTEM_USER()                         | 返回当前用户                                                 | `SELECT SYSTEM_USER(); -> guest@%`                           |
|                            USER()                            | 返回当前用户                                                 | `SELECT USER(); -> guest@%`                                  |
|                          VERSION()                           | 返回数据库的版本号                                           | `SELECT VERSION() -> 5.6.34`                                 |

### 自定义函数

```mysql
CREATE FUNCTION $funcName (
    [$arg $type]...
)
RETURN $type
BEGIN
	declare $varName $type default $defvalue;
	set $varName = $value;
	return ($value);
	# 函数内不支持SQL语句
END
```

## 存储过程

> &emsp;&emsp;存储过程（Stored Procedure）是一种在数据库中存储复杂程序，以便外部程序调用的一种数据库对象。
>
> &emsp;&emsp;存储过程是为了完成特定功能的SQL语句集，经编译创建并保存在数据库中，用户可通过指定存储过程的名字并给定参数(需要时)来调用执行。
>
> &emsp;&emsp;存储过程。是存储的一个操作过程，不是MySQL存储数据的过程。

### 创建存储过程

存储过程在SQL中用call调用，无返回值

```mysql
CREATE PROCEDURE $proceName (
    [in/out/inout $arg $type]...
    # in为参数，out为变量（通过set @$varName = $value定义）
)
BEGIN
	$SQLs...
	$fubctions...
END
```

> 阿里发话，这个咱不学了！！

## 用户操作与权限

一看就懂方法：编辑mysql\user

高级方法：

#### 用户操作

```mysql
create user username@ip identified by password;	# ip的通配符为%
drop user username@ip;
rename user old_username@ip to new_username@ip;
```

#### 权限管理

```mysql
show grants for username@ip;
grant permissions on databases.tables to username@ip;
revoke permissions on databases.tables from username@ip;
```
