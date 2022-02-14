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

#### 库

```mysql
show databases;
create database $databaseName engine=$engineName default $key $value;
drop database $databaseName;
use $database;
```

##### 数据库备份/恢复

```shell
mysqldump -u $username (-d) $database > $file -p $password	# 备份（-d：仅备份结构）
mysqldump -u $username $database < $file -p $password		# 恢复
```

#### 表

```mysql
show tables;
create table $tableNme($listName $dataType $characteristics) default $key=$value;
	default $value
	not null
delete from $tableName;					# 清空表
truncate table $tableName;				# 重置自增，快速
drop table $tableName;					# 删除表
alter table $tableName; 				# change table's setting variables.
```

#### 记录

##### 增

```mysql
insert into $tableName($listNames) values($values);	# 逐行插入
insert into $tableName($listNames) select $listNames from $sourceTable;	# 导入已有数据
```

##### 删

```mysql
delete from $tableName where $条件;
```

##### 改

```mysql
update $tableName set $key=$value where $条件;
```

##### 查

```mysql
select 
	function()
	$listHead 
	as $newListHead 
	from $table 
	group by $listName
	having $条件						# 对聚合结果二次排序
	where $条件 
	order by $listName desc/asc;			
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

#### 连表

> 将多张表以一定的关系拼接起来


```mysql
inner/full /left/right(outer) join $tableB on $tableA.$listA = $tableB.$listB
```

> - **inner**：内连接，取交集，采用先求笛卡尔积后筛选的方式操作
>
> - **left**：左连接，以左表为准
>
> - **right**：右连接，以右表为准
>
> - **full**：完全连接，取并集（MySQL不支持）
>
> - ##### **cross**，交叉连接，后无on语句（甚至可以不写cross join语句），与无on语句的内连接相同

#### 列的特殊属性

- **primary key**：主键，要求值唯一且不为空，表内只有一个主键，主键可由多个列组成

- **auto_increment**：自增，要求被设置为primary key且唯一

  > auto_increament：下一个记录中的值

  ```mysql
  show session variables like "auto_increament%";		# 查看会话变量
  set session auto_increament_increament = $value;	# 设置自增步长(会话)
  set global auto_increament_increament = $value;		# 设置自增步长（全局）
  set session auto_increament_offset = $value;		# 设置自增起始值(会话)
  set global auto_increament_offset = $value;			# 设置自增起始值（全局）
  ```

- **unique**：值唯一（可不完全为空），可加速查找

  ```mysql
  unique $name ($listNames)
  ```

- **default**：设置默认值

- **constraint**：约束

  - **foreign key**：外键，将某一列的值限制在一个可变范围内，相当于升级版enum（n对多）

    ```mysql
    constraint $constraintName foreign key ($listNames) references $tableName($primaryKeyName)
    ```

#### 临时表

```mysql
($data) as $yempTable
```

可通过临时表简化部分操作

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

### 日期

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

