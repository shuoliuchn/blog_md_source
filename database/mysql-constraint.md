## MySQL 表的完整性约束

[TOC]

### 概述

为了防止不符合规范的数据进入数据库，在用户对数据进行插入、修改、删除等操作时，DBMS 自动按照一定的约束条件对数据进行监测，使不符合规范的数据不能进入数据库，以确保数据库中存储的数据正确、有效、相容。 

约束条件与数据类型的宽度一样，都是可选参数，主要分为以下几种：

- UNSIGNED：无符号，搭配数值型数据使用
- ZEROFILL：使用 0 填充
- NOT NULL：非空约束，指定某列不能为空
- UNIQUE：唯一约束，指定某列或者几列组合不能重复
- PRIMARY KEY (PK)：主键，指定该列的值可以唯一地标识该列记录
- AUTO_INCREMENT：标识该字段的值自动增长（整数类型，而且为主键）
- FOREIGN KEY (FK)：外键，指定该行记录从属于主表中的一条记录，主要用于参照完整性

### unsigned 无符号

unsigned 主要是针对数值型数据进行的约束。经过 unsigned01约束后，将只能表示整数，但能表示数值的范围会翻倍。

创建一个 test_unsigned 数据表，表中的两个字段的类型分别为普通 int 整数和无符号的 int 整数：

```mysql
mysql> create table test_unsigned (id1 int,id2 int unsigned);
Query OK, 0 rows affected (0.04 sec)

mysql> desc test_unsigned;
+-------+------------------+------+-----+---------+-------+
| Field | Type             | Null | Key | Default | Extra |
+-------+------------------+------+-----+---------+-------+
| id1   | int(11)          | YES  |     | NULL    |       |
| id2   | int(10) unsigned | YES  |     | NULL    |       |
+-------+------------------+------+-----+---------+-------+
2 rows in set (0.01 sec)
```

给普通 int 插入负数，给无符号 int 插入正数时，是完全正常的：

```mysql
mysql> insert into test_unsigned value (-1,1);
Query OK, 1 row affected (0.01 sec)

mysql> select * from test_unsigned;
+------+------+
| id1  | id2  |
+------+------+
|   -1 |    1 |
+------+------+
1 row in set (0.00 sec)
```

但是如果给无符号的 int 插入负数，将会报错：

```mysql
mysql> insert into test_unsigned value (1,-1);
ERROR 1264 (22003): Out of range value for column 'id2' at row 1
```

如果给无符号的 int 插入一个超过 2 147 483 647 的数字，是可以的：

```mysql
mysql> insert into test_unsigned value (2147483647,2147483648);
Query OK, 1 row affected (0.01 sec)

mysql> select * from test_unsigned;
+------------+------------+
| id1        | id2        |
+------------+------------+
|         -1 |          1 |
| 2147483647 | 2147483648 |
+------------+------------+
2 rows in set (0.00 sec)
```

但是给普通的 int 插入一条超过  2 147 483 647 的数字就会报错：

```mysql
mysql> insert into test_unsigned value (2147483648,2147483647);
ERROR 1264 (22003): Out of range value for column 'id1' at row 1
```

### not null 非空约束

not null 用来约束字段中的数据是否可空，null 表示空，而非字符串

- not null：不可空
- null：可空 

创建一个数据表，约束字段为 not null：

```mysql
mysql> create table test_null (id int not null);
Query OK, 0 rows affected (0.03 sec)

mysql> desc test_null;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| id    | int(11) | NO   |     | NULL    |       |
+-------+---------+------+-----+---------+-------+
1 row in set (0.01 sec)
```

我们看到 Null 列显示为 NO，也就是不能为空。

如果我们插入一条空数据，会报错，插入普通整数则是可以的：

```mysql
mysql> insert into test_null value (null);
ERROR 1048 (23000): Column 'id' cannot be null
mysql> insert into test_null value (1);
Query OK, 1 row affected (0.00 sec)

mysql> select * from test_null;
+----+
| id |
+----+
|  1 |
+----+
1 row in set (0.00 sec)
```



如果向非空字段中插入空值成功了，可以配置严格模式来解决这个问题。

设置严格模式：

- 不支持对 not null 字段插入 null 值
- 不支持对自增长字段插入值
- 不支持 text 字段有默认值

直接在mysql中生效（重启失效）:

```mysql
mysql>set sql_mode="STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION";
```

配置文件添加（永久失效）：

```ini
sql-mode="STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION"
```

### default 设置默认值

如果某一列中经常有重复的内容（比如有的时候性别可能很单一），就需要我们频繁地插入，这样会给我们的操作带来新的负担，于是就出现了默认值的概念。

默认值，创建列时可以指定默认值，当插入数据时如果未主动设置，则自动添加默认值。

创建数据表，其中一个字段只有非空约束，另一个字段在非空的基础上设置默认值：

```mysql
mysql> create table test_default (id1 int not null, id2 int not null default 666);
Query OK, 0 rows affected (0.03 sec)

mysql> desc test_default;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| id1   | int(11) | NO   |     | NULL    |       |
| id2   | int(11) | NO   |     | 666     |       |
+-------+---------+------+-----+---------+-------+
2 rows in set (0.01 sec)
```

只给 id1 字段插入数据，id2 字段会自动被插入默认的数据：

```mysql
mysql> insert into test_default(id1) value (1);
Query OK, 1 row affected (0.00 sec)

mysql> select * from test_default;
+-----+-----+
| id1 | id2 |
+-----+-----+
|   1 | 666 |
+-----+-----+
1 row in set (0.00 sec)
```

如果也给 id2 传入数据，那么会以传入的值为准，默认数据会被覆盖：

```mysql
mysql> insert into test_default value (222,333);
Query OK, 1 row affected (0.01 sec)

mysql> select * from test_default;
+-----+-----+
| id1 | id2 |
+-----+-----+
|   1 | 666 |
| 222 | 333 |
+-----+-----+
2 rows in set (0.00 sec)
```

### unique 唯一约束

唯一约束，指定某列或者几列组合不能重复。

唯一约束有两种方式可以实现。

第一种方式是像前面的几个约束一样，直接在定义的字段后面加上 unique 实现：

```mysql
mysql> create table test_unique1 (id int unique);
Query OK, 0 rows affected (0.04 sec)

mysql> desc test_unique1;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| id    | int(11) | YES  | UNI | NULL    |       |
+-------+---------+------+-----+---------+-------+
1 row in set (0.01 sec)
```

第二种方式是当字段定义完成后，再声明字段唯一：

```mysql
mysql> create table test_unique2 (
    -> id int,
    -> unique(id)
    -> );
Query OK, 0 rows affected (0.04 sec)

mysql> desc test_unique2;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| id    | int(11) | YES  | UNI | NULL    |       |
+-------+---------+------+-----+---------+-------+
1 row in set (0.01 sec)
```

当第一次插入一个数据时，是正常的，但是如果再次插入相同的值，则会报错：

```mysql
mysql> insert into test_unique1 value (666);
Query OK, 1 row affected (0.01 sec)

mysql> select * from test_unique1;
+------+
| id   |
+------+
|  666 |
+------+
1 row in set (0.00 sec)

mysql> insert into test_unique1 value (666);
ERROR 1062 (23000): Duplicate entry '666' for key 'id'
```

#### 非空唯一

非空唯一就是联合使用 not null 和 unique 约束。非空唯一的字段既不可以为空，也不能重复：

```mysql
mysql> create table test_nnu (id int not null unique);
Query OK, 0 rows affected (0.03 sec)

mysql> desc test_nnu;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| id    | int(11) | NO   | PRI | NULL    |       |
+-------+---------+------+-----+---------+-------+
1 row in set (0.01 sec)
```

我们看到，Key 成了 PRI，也就是主键。主键是一种非空唯一的字段。如果不设置主键，第一个非空唯一字段会默认成为主键。

#### 联合唯一

联合唯一是两个字段的值不可以一起重复，但是单个字段是可以重复的：

```mysql
mysql> create table test_union_unique (
    -> id int,
    -> name char(20),
    -> host char(15) not null,
    -> port int not null,
    -> unique(host,port) # 联合唯一
    -> );
Query OK, 0 rows affected (0.04 sec)

mysql> desc test_union_unique;
+-------+----------+------+-----+---------+-------+
| Field | Type     | Null | Key | Default | Extra |
+-------+----------+------+-----+---------+-------+
| id    | int(11)  | YES  |     | NULL    |       |
| name  | char(20) | YES  |     | NULL    |       |
| host  | char(15) | NO   | PRI | NULL    |       |
| port  | int(11)  | NO   | PRI | NULL    |       |
+-------+----------+------+-----+---------+-------+
4 rows in set (0.01 sec)

mysql> insert into test_union_unique values
    -> (1,'nginx','192.168.0.10',80),
    -> (2,'haproxy','192.168.0.20',80),
    -> (3,'mysql','192.168.0.10',3306)
    -> ;
Query OK, 3 rows affected (0.01 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> select * from test_union_unique;
+------+---------+--------------+------+
| id   | name    | host         | port |
+------+---------+--------------+------+
|    1 | nginx   | 192.168.0.10 |   80 |
|    3 | mysql   | 192.168.0.10 | 3306 |
|    2 | haproxy | 192.168.0.20 |   80 |
+------+---------+--------------+------+
3 rows in set (0.00 sec)

mysql> insert into test_union_unique value (4,'nginx2','192.168.0.10',80);
ERROR 1062 (23000): Duplicate entry '192.168.0.10-80' for key 'host'
```

### PRIMARY KEY

主键为了确保该字段中的所有值都是非空且唯一的。换言之，主键用来独一无二地确认一个表格中的每一行数据。
主键可以包含一个字段或多个字段。当主键包含多个字段时，称为组合键（Composite Key），也称作联合主键。
主键可以在建置新表格时设定（运用 CREATE TABLE 语句），也可以通过改变现有的表格架构的方式设定（运用 ALTER TABLE）。
主键必须唯一，主键值非空；可以是单一字段，也可以是多字段组合。

#### 单字段主键

使用一个字段作为主键，称为但字段主键。

方法一：not null + unique

如果不手动设置，MySQL 默认会将第一个非空且唯一的字段设置为主键