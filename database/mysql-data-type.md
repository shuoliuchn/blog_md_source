# MySQL 中的数据类型

[TOC]

MySQL 中的数据类型主要有五种：数值类型、字符串类型、时间类型、枚举和集合、函数。接下来，我们将一一讨论。

## 数值类型

数字有两种，整数和小数，而整数和小数有都会有有符号和无符号两种状态。

MySQL 支持所有 SQL 标准的数据类型。这些类型包括严格数值数据类型（INTEGER、SMALLINT、DECIMAL 和 NUMERIC），以及近似数值数据类型（FLOAT、REAL 和 DOUBLE PRECISION）。

关键字 INT 是 INTEGER 的同义词，关键字 DEC 是 DECIMAL 的同义词。

MySQL 支持的整数类型有 TINYINT、MEDIUMINT 和 BIGINT。下面的表显示了需要的每个整数类型的存储和范围。

对于小数的表示，MySQL 分为两种方式：浮点数和定点数。浮点数包括 float（单精度）和 double（双精度）。而定点数只有 decimal 一种，在 MySQL 中以字符串的形式存放，比浮点数更精确，适合用来表示货币等精度高的数据。

我们常用到的整数数据类型有：tinyint 和 int。其中，tinyint 常用来表示年龄，而 int 用来指代其他的数字。

| 类型           | 大小                                                         | 范围（有符号）                                               | 范围（无符号）unsigned 约束                                  | 用途            |
| -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------- |
| TINYINT        | 1 字节                                                       | `[-128, 127]`                                                | `[0, 255]`                                                   | 小整数值        |
| SMALLINT       | 2 字节                                                       | `[-32 768, 32 767]`                                          | `[0, 65 535]`                                                | 大整数值        |
| MEDIUMINT      | 3 字节                                                       | `[-8 388 608, 8 388 607]`                                    | `[0, 16 777 215]`                                            | 大整数值        |
| INT 或 INTEGER | 4 字节                                                       | `[-2 147 483 648, 2 147 483 647]`                            | `[0, 4 294 967 295]`                                         | 大整数值        |
| BIGINT         | 8 字节                                                       | `[-9 233 372 036 854 775 808, 9 223 372 036 854 775 807]`    | `[0, 18 446 744 073 709 551 615]`                            | 极大整数值      |
| FLOAT          | 4 字节 `float(255, 30)`                                      | `[-3.402 823 466 E+38, -1.175 494 351 E-38]`，0，`[1.175 494 351 E-38, 3.402 823 466 351 E+38]` | 0，`[1.175 494 351 E-38, 3.402 823 466 E+38]`                | 单精度 浮点数值 |
| DOUBLE         | 8 字节 `double(255, 30)`                                     | `[-1.797 693 134 862 315 7 E+308, -2.225 073 858 507 201 4 E-308]`，0，`[2.225 073 858 507 201 4 E-308, 1.797 693 134 862 315 7 E+308]` | 0，`[2.225 073 858 507 201 4 E-308, 1.797 693 134 862 315 7 E+308]` | 双精度 浮点数值 |
| DECIMAL        | 对 `DECIMAL(M,D)` ，如果 `M > D`，为 `M + 2` 否则为 `D + 2double(65,30)` | 依赖于 M 和 D 的值                                           | 依赖于 M 和 D 的值                                           | 小数值          |

### int 整数

创建一个表 test_int，有两个字段，一个是普通 int 类型的 id，一个是限定宽度为 5 的 int 类型的 id2：

```mysql
create table test_int(id1 int, id2 int(5));
```

向 test_int 表中插入一组正常的数据，比如 `(1,1)`：

```mysql
mysql> insert into test_int value (1,1);
Query OK, 1 row affected (0.00 sec)
```

查询表中内容，没有任何异常：

```mysql
mysql> select * from test_int;
+------+------+
| id1  | id2  |
+------+------+
|    1 |    1 |
+------+------+
1 row in set (0.00 sec)
```

那么如果我们插入一条长度超过 5 的数据，结果如何呢？比如：

```mysql
insert into test_int value (111111,111111);
```

再次查询表中的数据：

```mysql
mysql> select * from test_int;
+--------+--------+
| id1    | id2    |
+--------+--------+
|      1 |      1 |
| 111111 | 111111 |
+--------+--------+
2 rows in set (0.00 sec)
```

仍然正常，并没有因为 id2 的长度限制而导致数据存储的异常。

原来，MySQL 中整型数字后面的长度限制，限制的是最小显示长度，存储时不受影响。

接下来，我们把 id1 的字段类型改成无符号的 int 类型：

```mysql
mysql> alter table test_int modify id1 int unsigned;
Query OK, 2 rows affected (0.11 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql>  desc test_int;
+-------+------------------+------+-----+---------+-------+
| Field | Type             | Null | Key | Default | Extra |
+-------+------------------+------+-----+---------+-------+
| id1   | int(10) unsigned | YES  |     | NULL    |       |
| id2   | int(5)           | YES  |     | NULL    |       |
+-------+------------------+------+-----+---------+-------+
2 rows in set (0.01 sec)
```

给 id1 插入一条大于 2147483647 的数据，是可以的：

```mysql
mysql> insert into test_int value (2147483648,2147483647);
Query OK, 1 row affected (0.00 sec)

mysql> select * from test_int;
+------------+------------+
| id1        | id2        |
+------------+------------+
|          1 |          1 |
|     111111 |     111111 |
| 2147483648 | 2147483647 |
+------------+------------+
3 rows in set (0.00 sec)
```

但是如果给 id2 插入一条大于 2147483647 的数据，会报错：

```mysql
mysql> insert into test_int value (2147483647,2147483648);
ERROR 1264 (22003): Out of range value for column 'id2' at row 1
```

### 小数

`float(m,n)` 的意思是总共存 m 位，小数点占 n 位。double 和 decimal 的位数约束也是这样的。

创建一个名为 test_decimal 的表，表中有三个字段，类型分别为 float、double 和 decimal，参数表示一共显示 5 位，小数部分占 2 位：

```mysql
mysql> create table test_decimal (id1 float(5,2),id2 double(5,2),id3 decimal(5,2));
Query OK, 0 rows affected (0.04 sec)
```

向表中插入数据 `(1.23,1.23,1.23)`，一切正常：

```mysql
mysql> insert into test_decimal value (1.23,1.23,1.23);
Query OK, 1 row affected (0.01 sec)

mysql> select * from test_decimal;
+------+------+------+
| id1  | id2  | id3  |
+------+------+------+
| 1.23 | 1.23 | 1.23 |
+------+------+------+
1 row in set (0.00 sec)
```

现在我们给三个字段都插入一条 1.234 的数据：

```mysql
mysql> insert into test_decimal value (1.234,1.234,1.234);
Query OK, 1 row affected, 1 warning (0.01 sec)

mysql> select * from test_decimal;
+------+------+------+
| id1  | id2  | id3  |
+------+------+------+
| 1.23 | 1.23 | 1.23 |
| 1.23 | 1.23 | 1.23 |
+------+------+------+
2 rows in set (0.00 sec)
```

我们发现，每个字段中，最后一位的 4 都被舍弃掉了。

如果插入的数据是 1.235：

```mysql
mysql> insert into test_decimal value (1.235,1.235,1.235);
Query OK, 1 row affected, 1 warning (0.00 sec)

mysql> select * from test_decimal;
+------+------+------+
| id1  | id2  | id3  |
+------+------+------+
| 1.23 | 1.23 | 1.23 |
| 1.23 | 1.23 | 1.23 |
| 1.24 | 1.24 | 1.24 |
+------+------+------+
3 rows in set (0.00 sec)
```

这时，会向前进一位。总结起来就是，小数的保留规则是四舍五入（不是四舍六入五成双）。

我们再创建一个表，取消每个字段长度的约束限制：

```mysql
mysql> create table test_decimal2 (id1 float,id2 double,id3 decimal);
Query OK, 0 rows affected (0.04 sec)
```

再给每个字段插入 1.234，查看效果：

```mysql
mysql> insert into test_decimal2 value (1.234,1.234,1.234);
Query OK, 1 row affected, 1 warning (0.01 sec)

mysql> select * from test_decimal2;
+-------+-------+------+
| id1   | id2   | id3  |
+-------+-------+------+
| 1.234 | 1.234 |    1 |
+-------+-------+------+
1 row in set (0.00 sec)
```

我们发现，float 和 double 都正常，而 decimal 默认值是整数。

当对小数位没有约束的时候，输入超长的小数，会发现 float 和 double 的区别：

```mysql
mysql> insert into test_decimal2 value (1.2355555555555555555555555555,1.2355555555555555555555555555,1.2355555555555555555555555555);
Query OK, 1 row affected, 1 warning (0.01 sec)

mysql> select * from test_decimal2;
+---------+--------------------+------+
| id1     | id2                | id3  |
+---------+--------------------+------+
|   1.234 |              1.234 |    1 |
| 1.23556 | 1.2355555555555555 |    1 |
+---------+--------------------+------+
2 rows in set (0.00 sec)
```

double 的精度显然是要高于 float 的。

## 日期和时间类型

MySQL 中表示时间值的日期和时间类型有：DATETIME、DATE、TIMESTAMP、TIME 和 YEAR。

每个时间类型有一个有效值范围和一个"零"值，当指定不合法的 MySQL 不能表示的值时使用"零"值。

每种时间类型的应用场景为

- datetime：年月日时分秒，开奖时间、聊天记录、账务、打卡时间
- date：发工资、生日、放假、入职离职时间、入学毕业时间
- time：上课时间、闹铃、体育项目/计算用时
- year：以年为单位做统计
- timestamp：时间戳，能表示的时间范围有限，已经不常用了

| 类型      | 大小 (字节) | 范围                                                         | 格式                | 用途                     |
| --------- | ----------- | ------------------------------------------------------------ | ------------------- | ------------------------ |
| DATE      | 3           | 1000-01-01/9999-12-31                                        | YYYY-MM-DD          | 年月日                   |
| TIME      | 3           | '-838:59:59'/'838:59:59'                                     | HH:MM:SS            | 时分秒                   |
| YEAR      | 1           | 1901/2155                                                    | YYYY                | 年份值                   |
| DATETIME  | 8           | 1000-01-01 00:00:00/9999-12-31 23:59:59                      | YYYY-MM-DD HH:MM:SS | 年月日时分秒             |
| TIMESTAMP | 4           | 1970-01-01 00:00:00/2038<br />结束时间是第 **2147483647** 秒，北京时间 **2038-1-19 11:14:07**，格林尼治时间 2038年1月19日 凌晨 03:14:07 | YYYYMMDD HHMMSS     | 混合日期和时间值，时间戳 |

### date、time 和 datetime

创建数据表 test_datetime，表中的三个字段分别是 date、time 和 datetime 类型：

```mysql
mysql> create table test_datetime (d date, t time, dt datetime);
Query OK, 0 rows affected (0.04 sec)

mysql> desc test_datetime;
+-------+----------+------+-----+---------+-------+
| Field | Type     | Null | Key | Default | Extra |
+-------+----------+------+-----+---------+-------+
| d     | date     | YES  |     | NULL    |       |
| t     | time     | YES  |     | NULL    |       |
| dt    | datetime | YES  |     | NULL    |       |
+-------+----------+------+-----+---------+-------+
3 rows in set (0.01 sec)
```

使用 now 函数可以获取当前时间。给三个字段都插入当前时间：

```mysql
mysql> insert into test_datetime value (now(),now(),now());
Query OK, 1 row affected, 1 warning (0.01 sec)

mysql> select * from test_datetime;
+------------+----------+---------------------+
| d          | t        | dt                  |
+------------+----------+---------------------+
| 2020-05-02 | 17:06:50 | 2020-05-02 17:06:50 |
+------------+----------+---------------------+
1 row in set (0.00 sec)
```

date 只显示日期，time 只显示时间，datetime 会显示日期和时间。

如果插入的数据为 null，也会全部显示为 null：

```mysql
mysql> insert into test_datetime value (null,null,null);
Query OK, 1 row affected (0.01 sec)

mysql> select * from test_datetime;
+------------+----------+---------------------+
| d          | t        | dt                  |
+------------+----------+---------------------+
| 2020-05-02 | 17:06:50 | 2020-05-02 17:06:50 |
| NULL       | NULL     | NULL                |
+------------+----------+---------------------+
2 rows in set (0.00 sec)
```

除了使用 now，我们还可以使用如下格式插入时间，仅以 datetime 为例：

```mysql
mysql> create table test_datetime2 (dt datetime);
Query OK, 0 rows affected (0.04 sec)

mysql> insert into test_datetime2 value ('2020-05-02 17:06:11');
Query OK, 1 row affected (0.01 sec)

mysql> insert into test_datetime2 value ('2020/05/02 17+06+11');
Query OK, 1 row affected (0.00 sec)

mysql> insert into test_datetime2 value ('20200502170611');
Query OK, 1 row affected (0.01 sec)

mysql> insert into test_datetime2 value (20200502170611);
Query OK, 1 row affected (0.01 sec)

mysql> select * from test_datetime2;
+---------------------+
| dt                  |
+---------------------+
| 2020-05-02 17:06:11 |
| 2020-05-02 17:06:11 |
| 2020-05-02 17:06:11 |
| 2020-05-02 17:06:11 |
+---------------------+
4 rows in set (0.00 sec)
```

### 设置时间默认值和自动更新

再创建表时，可以给表格默认值约束和自动更新约束：

```mysql
mysql> create table test_time (id int,dt datetime not null default current_timestamp on update current_timestamp);
Query OK, 0 rows affected (0.04 sec)

mysql> insert into test_time(id) value (1);
Query OK, 1 row affected (0.00 sec)

mysql> select * from test_time;
+------+---------------------+
| id   | dt                  |
+------+---------------------+
|    1 | 2020-05-02 17:37:19 |
+------+---------------------+
1 row in set (0.00 sec)

mysql> update test_time set id=2 where id=1;
Query OK, 1 row affected (0.01 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> select * from test_time;
+------+---------------------+
| id   | dt                  |
+------+---------------------+
|    2 | 2020-05-02 17:38:05 |
+------+---------------------+
1 row in set (0.00 sec)
```

插入数据时，会默认插入当前时间。修改数据时，会更新至修改时的时间。

## 字符串类型

字符串类型包括：CHAR、VARCHAR、BINARY、VARBINARY、BLOB 和 TEXT。

| 类型       | 大小                 | 用途                            |
| ---------- | -------------------- | ------------------------------- |
| CHAR       | 0-255 字符           | 定长字符串                      |
| VARCHAR    | 0-65535 字节         | 变长字符串                      |
| TINYBLOB   | 0-255 字节           | 不超过 255 个字符的二进制字符串 |
| TINYTEXT   | 0-255 字节           | 短文本字符串                    |
| BLOB       | 0-65 535 字节        | 二进制形式的长文本数据          |
| TEXT       | 0-65 535 字节        | 长文本数据                      |
| MEDIUMBLOB | 0-16 777 215 字节    | 二进制形式的中等长度文本数据    |
| MEDIUMTEXT | 0-16 777 215 字节    | 中等长度文本数据                |
| LONGBLOB   | 0-4 294 967 295 字节 | 二进制形式的极大文本数据        |
| LONGTEXT   | 0-4 294 967 295 字节 | 极大文本数据                    |

常用的字符串数据类型为 char 和 varchar，它们的区别和优缺点为：

- char：
  - 定长，最长 255 字节
  - 浪费空间
  - 存取快
- varchar：
  - 变长，最长 65535 字节
  - 节省空间
  - 存取慢

char 常用于存储手机号码、身份证号、用户名、密码等长度固定或需要频繁存取的数据。而 varchar 常用于存储评论、发表的微博等长度不固定的数据。

创建一个名为 test_char 的数据表，表中两个字段的数据类型分别为 char 和 varchar，长度都限制为 4：

```mysql
mysql> create table test_char (c char(10),v varchar(10));
Query OK, 0 rows affected (0.04 sec)
```

插入普通数据就不演示了，我们插入一个有很多空格的字符串，比如 `'  a b  '`：

```mysql
mysql> insert into test_char value ('  a b  ', '  a b  ');
Query OK, 1 row affected (0.00 sec)

mysql> select * from test_char;
+-------+---------+
| c     | v       |
+-------+---------+
|   a b |   a b   |
+-------+---------+
1 row in set (0.00 sec)
```

我们可以大致看到，char 的后面的空格默认是被裁剪掉了，而 varchar 不会被清除。

我们计算查询结果的长度，可以验证结论：

```mysql
mysql> select length(c),length(v) from test_char;
+-----------+-----------+
| length(c) | length(v) |
+-----------+-----------+
|         5 |         7 |
+-----------+-----------+
1 row in set (0.01 sec)
```

char 比 varchar 少了两个字符，就是因为 char 的结尾空格被清除了。

还可以在输入内容的开头和结尾加上一个 `+`，看起来更清楚些：

```mysql
mysql> select concat('+',c,'+'),concat('+',v,'+') from test_char;
+-------------------+-------------------+
| concat('+',c,'+') | concat('+',v,'+') |
+-------------------+-------------------+
| +  a b+           | +  a b  +         |
+-------------------+-------------------+
1 row in set (0.00 sec)
```

我们说，char 会清除掉后面的空格，而 varchar 不会，这是建立在字符串的长度不超过限制的基础上。如果字符串的长度超过 varchar 的限制，varchar 也会清楚末尾的空格，直到和限定的长度一致：

```mysql
mysql> insert into test_char value ('abcdefgh     ', 'abcdefgh     ');
Query OK, 1 row affected, 1 warning (0.01 sec)

mysql> select c,v,length(c),length(v) from test_char;
+----------+------------+-----------+-----------+
| c        | v          | length(c) | length(v) |
+----------+------------+-----------+-----------+
|   a b    |   a b      |         5 |         7 |
| abcdefgh | abcdefgh   |         8 |        10 |
+----------+------------+-----------+-----------+
2 rows in set (0.00 sec)
```

BINARY 和 VARBINARY 类似于 CHAR 和 VARCHAR，不同的是它们包含二进制字符串而不要非二进制字符串。也就是说，它们包含字节字符串而不是字符字符串。这说明它们没有字符集，并且排序和比较基于列值字节的数值值。

BLOB 是一个二进制大对象，可以容纳可变数量的数据。有 4 种 BLOB 类型：TINYBLOB、BLOB、MEDIUMBLOB 和 LONGBLOB。它们区别在于可容纳存储范围不同。

有 4 种 TEXT 类型：TINYTEXT、TEXT、MEDIUMTEXT 和 LONGTEXT。对应的这 4 种 BLOB 类型，可存储的最大长度不同，可根据实际情况选择。

## ENUM 和 SET 类型

ENUM 中文名称叫枚举类型，它的值范围需要在创建表时通过枚举方式显示。ENUM **只允许从值集合中选取单个值，而不能一次取多个值**。

SET 和 ENUM 非常相似，也是一个字符串对象，里面可以包含 0-64 个成员。根据成员的不同，存储上也有所不同。set 类型可以**允许值集合中任意选择 1 或多个元素进行组合**。对超出范围的内容将不允许注入，而对重复的值将进行自动去重。

简言之，enum 枚举是单选方法，比如性别；set 集合是多选方法，可以去重，比如爱好。

| 类型 | 大小                                                         | 用途           |
| ---- | ------------------------------------------------------------ | -------------- |
| ENUM | 对 1-255 个成员的枚举需要 1 个字节存储；<br />对于 255-65535 个成员，需要 2 个字节存储；<br />最多允许 65535 个成员。 | 单选：选择性别 |
| SET  | 1-8 个成员的集合，占 1 个字节<br />9-16 个成员的集合，占 2 个字节<br />17-24 个成员的集合，占 3 个字节<br />25-32 个成员的集合，占 4 个字节<br />33-64 个成员的集合，占 8 个字节 | 多选：兴趣爱好 |

### enum 枚举

创建一个数据表 test_enum，字段的类型分别为 enum：

```mysql
mysql> create table test_enum (gender enum('male','female'));
Query OK, 0 rows affected (0.04 sec)

mysql> desc test_enum;
+--------+-----------------------+------+-----+---------+-------+
| Field  | Type                  | Null | Key | Default | Extra |
+--------+-----------------------+------+-----+---------+-------+
| gender | enum('male','female') | YES  |     | NULL    |       |
+--------+-----------------------+------+-----+---------+-------+
1 row in set (0.01 sec)
```

如果插入的数据为 male 和 female 之一，可以正常插入：

```mysql
mysql> insert into test_enum value ('male');
Query OK, 1 row affected (0.00 sec)

mysql> select * from test_enum;
+--------+
| gender |
+--------+
| male   |
+--------+
1 row in set (0.00 sec)
```

但如果同时插入 male 和 female 或者插入这之外的数据，是不成的：

```mysql
mysql> insert into test_enum value ('male,female');
ERROR 1265 (01000): Data truncated for column 'gender' at row 1
mysql> insert into test_enum value ('man');
ERROR 1265 (01000): Data truncated for column 'gender' at row 1
```

### set 集合

创建一个名为 test_set 的数据表，字段的数据类型为 set：

```mysql
mysql> create table test_set (hobby set('抽烟','喝酒','烫头'));
Query OK, 0 rows affected (0.03 sec)

mysql> desc test_set;
+-------+---------------------------------+------+-----+---------+-------+
| Field | Type                            | Null | Key | Default | Extra |
+-------+---------------------------------+------+-----+---------+-------+
| hobby | set('抽烟','喝酒','烫头')       | YES  |     | NULL    |       |
+-------+---------------------------------+------+-----+---------+-------+
1 row in set (0.01 sec)
```

可以插入抽烟、喝酒和烫头中的任意数量项，并自带去重功能：

```mysql
mysql> insert into test_set value('抽烟,烫头,抽烟');
Query OK, 1 row affected (0.00 sec)

mysql> select * from test_set;
+---------------+
| hobby         |
+---------------+
| 抽烟,烫头     |
+---------------+
1 row in set (0.00 sec)
```

不过如果输入不属于选项中的内容，会报错：

```mysql
mysql> insert into test_set value('抽烟,烫头,洗澡');
ERROR 1265 (01000): Data truncated for column 'hobby' at row 1
```

