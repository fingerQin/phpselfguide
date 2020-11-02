## 0x08.3 索引

索引是 `MySQL` 数据库中的重要对象之一，用于快速找出某个列中有某一特定值的行。在 `MySQL` 中，**索引**（`index`）也叫做“键（`key`）”，它**是**存储引擎用于快速找到记录的一种**数据结构**。

### 1）为什么要使用索引？

索引是 `MySQL` 中一种十分重要的数据库对象。它是数据库性能调优技术的基础，常用于实现数据的快速检索。

索引就是根据表中的一列或若干列按照一定顺序建立的列值与记录行之间的对应关系表，实质上是一张描述索引列的列值与原表中记录行之间一一对应关系的有序表。



### 2）数据读取顺序

在 `MySQL 中`，通常有以下两种方式访问数据库表的行数据：

#### 1) 顺序访问

顺序访问是在表中实行全表扫描，从头到尾逐行遍历，直到在无序的行数据中找到符合条件的目标数据。这种方式实现比较简单，但是当表中有大量数据的时候，效率非常低下。例如，在几千万条数据中查找少量的数据时，使用顺序访问方式将会遍历所有的数据，花费大量的时间，显然会影响数据库的处理性能。

#### 2) 索引访问

索引访问是通过遍历索引来直接访问表中记录行的方式。使用这种方式的前提是对表建立一个索引，在列上创建了索引之后，查找数据时可以直接根据该列上的索引找到对应记录行的位置，从而快捷地查找到数据。索引存储了指定列数据值的指针，根据指定的排序顺序对这些指针排序。

例如，在学生基本信息表 `students` 中，如果基于 `student_id` 建立了索引，系统就建立了一张索引列到实际记录的映射表，当用户需要查找 `student_id` 为 `12022` 的数据的时候，系统先在 `student_id` 索引上找到该记录，然后通过映射表直接找到数据行，并且返回该行数据。因为扫描索引的速度一般远远大于扫描实际数据行的速度，所以采用索引的方式可以大大提高数据库的工作效率。



### 3）索引分类

#### 3.1）主键索引（PRIMARY KEY）

也简称主键。它可以提高查询效率，并提供唯一性约束。一张表中只能有一个主键。被标志为自动增长的字段一定是主键，但主键不一定是自动增长。一般把主键定义在无意义的字段上（如：编号），主键的数据类型最好是数值。

主键创建示例：

```sql
ALTER TALBE tb_emp1 ADD PRIMARY KEY (id) USING BTREE;
```

通过 `ALTER TABLE` 命令为 `tb_emp1` 表创建了一个使用 `BTREE` 索引算法的主键。



#### 3.2）单列索引

单列索引也俗称普通索引。它是在单一字段上施加的普通索引。

示例 1：

```sql
ALTER TABLE tb_emp1 ADD INDEX name_idx (`name`) USING BTREE;
```

示例 2（指定前缀长度）：

```sql
ALTER TABLE tb_emp1 ADD INDEX name_idx (`name`(10)) USING BTREE;
```



#### 3.3）联合索引

联合索引也有人叫组合索引。它是在两个或两个以上的字段加施加的索引方式。

示例 1：

```sql
ALTER TABLE tb_emp1 ADD INDEX name_ctime_idx (`name`, `c_time`) USING BTREE;
```

示例 2（指定前缀长度）：

```sql
ALTER TABLE tb_emp1 ADD INDEX name_ctime_idx (`name`(10), `c_time`) USING BTREE;
```



#### 3.4）唯一索引

主键索引与单列索引的区别有两个：

- 1）唯一索引同一条记录只允许出现一次。
- 2）唯一索引的索引数据在扫描时得到就马上返回。单列索引还会继续向下扫描。唯一索引性能稍微好一些。主要取决于单列索引的数据量。

```sql
ALTER TABLE tb_emp1 ADD UNIQUE INDEX (`name`) USING BTREE;
```



#### 3.5）全文索引

旧版的 `MySQL` 的全文索引只能用在 `MyISAM` 表格的 `char`、`varchar` 和 `text` 的字段上。
不过新版的 `MySQL5.6.24`上 `InnoDB` 引擎也加入了全文索引。

示例：

```sql
ALTER TABLE tb_emp1 ADD FULLTEXT INDEX(name);
```



### 4）索引管理

`MySQL` 提供了三种创建索引的方法：

#### 4.1）使用 CREATE INDEX 语句

可以使用专门用于创建索引的 `CREATE INDEX` 语句在一个已有的表上创建索引，但该语句不能创建主键。

语法格式：

```sql
CREATE INDEX <索引名> ON <表名> (<列名> [<长度>] [ ASC | DESC])
```

语法说明如下：

- `<索引名>`：指定索引名。一个表可以创建多个索引，但每个索引在该表中的名称是唯一的。
- `<表名>`：指定要创建索引的表名。
- `<列名>`：指定要创建索引的列名。通常可以考虑将查询语句中在 `JOIN` 子句和 `WHERE` 子句里经常出现的列作为索引列。
- `<长度>`：可选项。指定使用列前的 `length` 个字符来创建索引。使用列的一部分创建索引有利于减小索引文件的大小，节省索引列所占的空间。在某些情况下，只能对列的前缀进行索引。索引列的长度有一个最大上限 255 个字节（`MyISAM` 和 `InnoDB` 表的最大上限为 1000 个字节），如果索引列的长度超过了这个上限，就只能用列的前缀进行索引。另外，`BLOB` 或 `TEXT` 类型的列也必须使用前缀索引。
- `ASC|DESC`：可选项。`ASC` 指定索引按照升序来排列，`DESC` 指定索引按照降序来排列，默认为`ASC`。



**示例1（单列索引）：**

```sql
CREATE INDEX name_idx ON tb_emp1 (name);
```

我们给 `tb_emp1` 表创建了一个名为 `name_idx` 的索引。该索引建立在表字段 `name` 上。默认的排序是 `ASC`。



**示例2（联合索引）：**

```sql
CREATE INDEX name_ctime_idx ON tb_emp1 (name,c_time);
```

在上面的基础上，我们创建了一个联合索引。所谓联合索引指的是在多个字段上创建一个索引。



**示例3（前缀索引）**

```sql
CREATE UNIQUE INDEX name_idx ON tb_emp1 (name(10));
```



> 通常我们不使用此种方法来创建索引。因为不够灵活且功能不够强大。



#### 4.2）使用 ALTER TABLE 语句

`CREATE INDEX` 语句可以在一个已有的表上创建索引，`ALTER TABLE` 语句也可以在一个已有的表上创建索引。在使用 `ALTER TABLE` 语句修改表的同时，可以向已有的表添加索引。具体的做法是在 `ALTER TABLE` 语句中添加以下语法成分的某一项或几项。

**示例1（主键索引）：**

```sql
ALTER TABLE tb_emp1 ADD PRIMARY KEY (id);
```

其中 `id` 是被加的主键索引字段。必须是整型。



 **示例2（唯一索引）**

```sql
ALTER TABLE tb_emp1 ADD UNIQUE (name);
```



**示例3（单列索引）**

```sql
ALTER TABLE tb_emp1 ADD INDEX name_idx (name);
```



**示例4（联合索引）**

```sql
ALTER TABLE tb_emp1 ADD INDEX name_ctime_idx (name, c_time);
```



**示例5（全文索引）**

```sql
ALTER TABLE tb_emp1 ADD FULLTEXT(name);
```

全文索引只能在 `MyISAM` 存储引擎上生效。



**示例6（前缀索引）**

```sql
ALTER TABLE tb_emp1 ADD INDEX name_idx (name(10));
```



**示例7（指定索引排序）**

```sql
ALTER TABLE tb_emp1 ADD INDEX name_idx (name(10) desc);
```



#### 4.3）使用 CREATE TABLE 语句

索引也可以在创建表（`CREATE TABLE`）的同时创建。在 `CREATE TABLE` 语句中添加以下语句。

示例：

```sql
CREATE TABLE tb_emp2(
    id int(11) UNSIGNED AUTO_INCREMENT COMMENT '员工ID。自增。',
    `name` varchar(25) NOT NULL COMMENT '员工姓名',
    deptId int(11) UNSIGNED NOT NULL DEFAULT '0' COMMENT '所在部门编号',
    job_num char(6) NOT NULL COMMENT '工号',
    salary FLOAT NOT NULL DEFAULT '0.00' COMMENT '工资',
	c_time datetime NOT NULL COMMENT '创建时间',
    primary key(id),
    key name_idx (`name`),
    key name_ctime_idx (`name`, `c_time`),
    UNIQUE unique_job_num (job_num)
) ENGINE = InnoDB DEFAULT CHARSET utf8mb4 DEFAULT COLLATE utf8mb4_unicode_ci COMMENT '员工表';
```



#### 4.4）查看索引

在 `MySQL` 中，如果要查看已创建的索引的情况，可以使用 `SHOW INDEX` 语句查看表中创建的索引。

语法格式：

```sql
SHOW INDEX FROM <表名> [ FROM <数据库名>]
```

语法说明如下：

- `<表名>`：要显示索引的表。
- `<数据库名>`：要显示的表所在的数据库。

**示例1（仅指定表）：**

```sql
SHOW INDEX FROM tb_emp2;
```

**示例2（指定数据库）：**

```sql
SHOW INDEX FROM tb_emp2 FROM test;
```



该语句会返回一张结果表，该表有如下几个字段，每个字段所显示的内容说明如下。

- `Table`：表的名称。
- `Non_unique`：用于显示该索引是否是唯一索引。若不是唯一索引，则该列的值显示为 1；若是唯一索引，则该列的值显示为 0。
- `Key_name`：索引的名称。
- `Seq_in_index`：索引中的列序列号，从 1 开始计数。
- `Column_name`：列名称。
- `Collation`：显示列以何种顺序存储在索引中。在 `MySQL` 中，升序显示值 “A”（升序），若显示为 `NULL`，则表示无分类。
- `Cardinality`：显示索引中唯一值数目的估计值。基数根据被存储为整数的统计数据计数，所以即使对于小型表，该值也没有必要是精确的。基数越大，当进行联合时，`MySQL` 使用该索引的机会就越大。
- `Sub_part`：若列只是被部分编入索引，则为被编入索引的字符的数目。若整列被编入索引，则为 `NULL`。
- `Packed`：指示关键字如何被压缩。若没有被压缩，则为 `NULL`。
- `Null`：用于显示索引列中是否包含 `NULL`。若列含有 `NULL`，则显示为 `YES`。若没有，则该列显示为 `NO`。
- `Index_type`：显示索引使用的类型和方法（`BTREE`、`FULLTEXT`、`HASH`、`RTREE`）。
- `Comment`：显示评注。



示例：

```
mysql> show index from tb_emp2\G;
*************************** 1. row ***************************
        Table: tb_emp2
   Non_unique: 0
     Key_name: PRIMARY
 Seq_in_index: 1
  Column_name: id
    Collation: A
  Cardinality: 0
     Sub_part: NULL
       Packed: NULL
         Null: 
   Index_type: BTREE
      Comment: 
Index_comment: 
*************************** 2. row ***************************
        Table: tb_emp2
   Non_unique: 0
     Key_name: unique_job_num
 Seq_in_index: 1
  Column_name: job_num
    Collation: A
  Cardinality: 0
     Sub_part: NULL
       Packed: NULL
         Null: 
   Index_type: BTREE
      Comment: 
Index_comment: 
*************************** 3. row ***************************
        Table: tb_emp2
   Non_unique: 1
     Key_name: name_idx
 Seq_in_index: 1
  Column_name: name
    Collation: A
  Cardinality: 0
     Sub_part: NULL
       Packed: NULL
         Null: 
   Index_type: BTREE
      Comment: 
Index_comment: 
*************************** 4. row ***************************
        Table: tb_emp2
   Non_unique: 1
     Key_name: name_ctime_idx
 Seq_in_index: 1
  Column_name: name
    Collation: A
  Cardinality: 0
     Sub_part: NULL
       Packed: NULL
         Null: 
   Index_type: BTREE
      Comment: 
Index_comment: 
*************************** 5. row ***************************
        Table: tb_emp2
   Non_unique: 1
     Key_name: name_ctime_idx
 Seq_in_index: 2
  Column_name: c_time
    Collation: A
  Cardinality: 0
     Sub_part: NULL
       Packed: NULL
         Null: 
   Index_type: BTREE
      Comment: 
Index_comment: 
5 rows in set (0.00 sec)
```



#### 4.5）删除索引

当不再需要索引时，可以使用 DROP INDEX 语句或 ALTER TABLE 语句来对索引进行删除。

##### 4.5.1) 使用 DROP INDEX 语句

语法格式：

```sql
DROP INDEX <索引名> ON <表名>
```

语法说明如下：

- `<索引名>`：要删除的索引名。
- `<表名>`：指定该索引所在的表名。

示例：

```sql
DROP INDEX name_idx ON tb_emp1;
```



##### 4.5.2) 使用 ALTER TABLE 语句

根据 `ALTER TABLE` 语句的语法可知，该语句也可以用于删除索引。具体使用方法是将 `ALTER TABLE` 语句的语法中部分指定为以下子句中的某一项。

- `DROP PRIMARY KEY`：表示删除表中的主键。一个表只有一个主键，主键也是一个索引。
- `DROP INDEX index_name`：表示删除名称为 `index_name` 的索引。
- `DROP FOREIGN KEY fk_symbol`：表示删除外键。

> 注意：如果删除的列是索引的组成部分，那么在删除该列时，也会将该列从索引中删除；如果组成索引的所有列都被删除，那么整个索引将被删除。



示例：

```sql
ALTER TABLE tb_emp1 DROP INDEX name_idx;
```



### 5）索引生效与失效场景

有了索引，不正确的使用方式，也会导致索引用不上。

在 SQL 语句中 `=`、`>`、`>=`、`<`、`<=`、`IN`、`BETWEEN AND`、`LIKE` 前缀 等条件应用在索引上时可以用上索引。其他的 `!=`、`<>`、`NOT IN`、`IS NULL`、`IS NOT NULL`、`LIKE %XXX%` 等形式则用不上索引。

除了这些情况，还如其他情况。我们现在就来一起分析索引失效的场景吧。



##### 5.1）索引无法存储 NULL 值

- 单列索引无法存储 `null` 值。联合索引无法存储全为 null 的值。
- 查询时，采用 `is null` 条件时，不能利用索引，只能全表扫描。

> 索引是有序的。`NULL` 代表的是不存在的意思。如果想把空值存入索引。可以在存入表的时候，将其转换成一个实体值。或者，建议一个联合索引。



##### 5.2）不适合键值较少的列

所谓键值较少指的是字段对应的列的数据重复数据较多。比如，我们常见的用户表的性别。一般只会有三个值：男、女、未知。那么，对于海量数据来说，它依旧只有三个值。在这种字段上创建索引是没有任何意思的。并且 `MySQL` 的 查询优化器也会有一个估算算法。如果重复数据超过一定的比例，索引会失效。



##### 5.3）OR 联接条件

如果在 `where` 条件中有 `or`，即使其中有条件带索引也不会使用。

> 要想使用 or，又想让索引生效，只能将 or 条件中的每个列都加上索引。



##### 5.4）联合索引不使用第一部分索引

当我们要想用上联合索引的时候。如果只能满足联合索引中非第一个字段的其他字段。则也不能用上索引。



##### 5.5）LIKE 查询以 `%` 开头

这个与 5.4 一样的道理。索引是顺序型的。所以，没有开头作为索引的入口，那就会导致索引无法工作。



##### 5.6）字符串类型索引值必须加引号

如果列类型是字符串，那一定要在条件中的数据使用引号引用起来。否则索引会失效。

> 一个良好的习惯：不管在何时情况下，都给条件后面的值加上引号。



##### 5.7）索引列进行函数/数学运算

当我们对已建立索引的字段进行数学运算或函数操作之后，索引将失效。



##### 5.8）B-Tree 与 Hash 两种索引对数据查询影响

`B` 树索引具有范围查找和前缀查找的能力。对于有 `N` 节点的 `B` 树，检索一条记录的复杂度为 `O(LogN)`。相当于二分查找。

哈希索引只能做等于查找，但是无论多大的 `Hash` 表，查找复杂度都是`O(1)`。

显然，如果值的差异性大，并且以等值查找（`=`、 `<`、`>`、`IN`）为主，`Hash` 索引是更高效的选择，它有 `O(1)`的查找复杂度。

如果值的差异性相对较差，并且以范围查找为主，`B` 树是更好的选择，它支持范围查找。



### 6）索引算法

索引一般有四种索引算法。

它们分别是：`BTREE`、`RTREE`、`HASH`、`FULLTEXT`。

`BTREE` 索引就是一种将索引值按一定的算法，存入一个树形的数据结构中。
`BTREE` 又分两种，一种是 `B-TREE`。如下图：

![](..\images\mysql_index.png)



另外一种是B+TREE，结构如下图：

![](..\images\mysql_index2.png)



`B+Tree` 相对于 `B-Tree` 有几点不同：

- 非叶子节点只存储键值信息。
- 所有叶子节点之间都有一个链指针。
- 数据记录都存放在叶子节点中。



`BTREE` 在 `MyISAM` 里的形式和 `Innodb` 稍有不同。
在 `Innodb` 里，有两种形态：一是 `primary key` 形态，其 `leaf node` 里存放的是数据，而且不仅存放了索引键的数据，还存放了其他字段的数据。二是 `secondary index`，其 `leaf node` 和普通的 `BTREE` 差不多，只是还存放了指向主键的信息.
而在 `MyISAM` 里，主键和其他的并没有太大区别。不过和 `Innodb` 不太一样的地方是在 `MyISAM` 里，`leaf node` 里存放的不是主键的信息，而是指向数据文件里的对应数据行的信息.