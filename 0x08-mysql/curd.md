## 0x08.2 基础知识

本节知识点：创建/编辑/删除（数据库）、创建/编辑/删除（表）、设置数据库字符编码、创建/编辑/删除（索引）等。



### 1）数据库操作

#### 1.1）创建数据库

我接触过很多经验尚浅的 PHP 开发者(1~3年)。发现大多数人都不会使用命令行来创建数据库。几乎都是通过 `Navicat for MySQL` 或者 `PHPMyAdmin` 的 UI 界面来操作数据库。当然，这样操作也无可厚非。但是，依赖工具对未来自身技能的提升会有限限制。特别是当自己要主导一个项目的时候，更多的是通过命令来操作数据库。



**语法：**

```sql
CREATE DATABASE [IF NOT EXISTS] <数据库名>
[[DEFAULT] CHARACTER SET <字符集名>] 
[[DEFAULT] COLLATE <校对规则名>];
```



不管您用何种 MySQL 客户端工具，希望您能用命令行窗口进行操作。

```sql
CREATE DATABASE dbname; 第一种
CREATE DATABASE IF NOT EXISTS dbname; 第二种
CREATE DATABASE dbname DEFAULT CHARACTER SET utf8mb4; 第三种
CREATE DATABASE dbname DEFAULT CHARACTER SET utf8mb4 DEFAULT COLLATE 'utf8mb4_general_ci'; 第四种
```



**第一种：**

```sql
CREATE DATABASE dbname;
```

`dbname` 是您想创建的数据库名称。`MySQL` 的数据存储区将以目录方式表示 `MySQL` 数据库，因此数据库名称必须符合操作系统的文件夹命名规则，不能以数字开头，尽量要有实际意义。注意在 `MySQL` 中不区分大小写。

我们这里未指定数据库的默认字符集编码以及字符集的默认校对规则。那么，它们从哪来呢？

**默认字符集编码**

当我们没有指定数据库默认字符集编码的时候，它将由数据库配置中的 `character_set_database` 决定。在大多数版本它的默认值为`latin1` 字符符（拉丁字符集）。但是由于我们中文字符不属于拉丁系字符集，就会导致绝大部分文字信息数据无法以拉丁字符集存储到数据库中。就会造成我们常说的**乱码**。

> 所以，不推荐创建数据库的时候不指定字符集编码。



**第二种：**

```sql
CREATE DATABASE IF NOT EXISTS dbname;
```

在第一种的基础上我们增加了 `IF NOT EXISTS` 。在创建数据库之前进行判断，只有该数据库目前尚不存在时才能执行操作。此选项可以用来避免数据库已经存在而重复创建的错误。



**第三种：**

```sql
CREATE DATABASE dbname DEFAULT CHARACTER SET utf8mb4;
```

我们在创建数据库的时候，同时指定了数据库的字符集编码。就目前而言，为了国际化字符的存储，建议直接上 `utf8mb4` 编码。对 `UTF8` 编码熟悉的人都知道它使用 3 个字节来存储一个字符。像 `GBK` 与 `GB2312` 是用 2 个字节来存储一个字符，拉丁是使用 1 个字节来存储 1 个字符。但是，拉丁只能存储拉丁字符，`GBK` 或 `GB2312` 只能存储中文字符以及其他单字节字符。而 `utf8mb4` 编码是在 `utf8` 基础上再增加一个字节来存储那些 4 个字节的特殊字符。比如，表情符。

> 所以，如果没有特殊要求。请一定使用 `UTF8` 字符集编码。

千万别为了省一个字节，导致很多非中方的多字节字符无法存储到数据库中出现乱码。

> `utf8mb4` 是解决一些特殊字符（4 个字节的宽字符）不能存储到 `utf8` 三个字节表示的字符集数据库中出现乱码的问题。



**第四种(面试要点)：**

```sql
CREATE DATABASE dbname DEFAULT CHARACTER utf8mb4 DEFAULT COLLATE 'utf8mb4_unicode_ci';
```

在第三种的基础上，我们增加了 `DEFAULT COLLATE`。即指定字符集的校对规则。当我们不指定的时候，默认值将由数据库配置中的 `collation_database` 决定。

什么是字符集校对规则？

**字符集内用于比较字符的一套规则。**

以 `utf8` 字符集编码做简单说明，字符集校对规则的作用。

- `utf8mb4_general_ci` : 存储在数据库中的字符不区分大小写。`ci` 即 `case insensitive`。比如，邮箱与用户名。特别是邮箱。对于 `Abc@xx.com` 与 `abc@xx.com` 实际是属于同一邮箱。如果区分大小写。当我们直接搜索 `abc@xx.com` 是无法搜索到 `Abc@xx.com` 邮箱的。这也是笔者当初面试一家游戏公司时，一技术负责人的面试点。创建数据库时指定的校对规则是全局性的。也可以单独针对表字段设置。
- `utf8mb4_bin` : 将每个字符以二进制存储且区分大小写。
- `utf8mb4_general_cs` : 它与 `utf8mb4_general_ci` 的唯一区别就是它区分大小写。
- `utf8mb4_unicode_ci` : 是基于标准的`Unicode`来排序和比较，能够在各种语言之间精确排序，`Unicode`排序规则为了能够处理特殊字符的情况，实现了略微复杂的排序算法。



> 那么我们该如何选择校对规则呢？

字符除了存储、还需要排序以及比较大小。`ut8mb4` 对应的排序字符集常用的有 `utf8mb4_unicode_ci`、`utf8mb4_general_ci`。



我们主要从排序准确怀与性能来比较：

**排序准确性：**

- `utf8mb4_unicode_ci` :  是基于标准的 `Unicode` 来排序和比较，能够在各种语言之间精确排序。
- `utf8mb4_general_ci` : 没有实现 `Unicode` 排序规则。在遇到某些特殊语言或字符是，排序结果可能不是所期望的。
  但是在绝大多数情况下，这种特殊字符的顺序一定要那么精确吗。比如 `Unicode`把`ß`、`Œ`当成`ss`和`OE`来看；而`general`会把它们当成`s`、`e`，再如`ÀÁÅåāă`各自都与 `A` 相等。

> 结论：`utf8mb4_unicode_ci` 排序准确性大于 `utf8mb4_general_ci`。



**性能：**

- `utf8mb4_unicode_ci` : 为了实现复杂字符的精确排序，所以性能略输 `utf8mb4_general_ci`。但是在如今 `CPU` 性能面前，它可以忽略。
- `utf8mb4_general_ci` : 在比较和排序的时候更快。

> 结论：`utf8mb4_unicode_ci` 性能略输 `utf8mb4_general_ci`。但在当今 `CPU` 性能面前，可以忽略不计此损失。



> 字符集校对规则：推荐使用 `utf8mb4_unicode_ci`。



#### 1.2）编辑数据库

这个很好理解。对已存在的数据进行编辑。与创建数据库类似。它的语法如下：

```sql
ALTER DATABASE [数据库名] { 
[ DEFAULT ] CHARACTER SET <字符集名> |
[ DEFAULT ] COLLATE <校对规则名>}
```



##### 1.2.1）更改数据库名称

```sql
ALTER DATABASE dbname RENAME TO newname;
```

这个语法比较简单。将 `dbname` 更改为 `newname` 名称。



##### 1.2.2）更改数据库默认字符集编码

```sql
ALTER DATABASE dbname DEFAULT CHARACTER SET ut8mb4;
```



##### 1.2.3）更改数据库默认字符集校对规则

```sql
ALTER DATABASE dbname DEFAULT COLLATE utf8mb4_unicode_ci;
```



#### 1.3）删除数据库

语法：

```sql
DROP DATABASE [ IF EXISTS ] <数据库名>
```

示例：

```sql
DROP DATABASE dbname;
DROP DATABASE IF EXISTS dbname;
```



> 注意：`MySQL` 安装后，系统会自动创建名为 `information_schema` 和 `mysql` 的两个系统数据库，系统数据库存放一些和数据库相关的信息，如果删除了这两个数据库，`MySQL` 将不能正常工作。



#### 1.4）查看数据库

有时候，我们想知道当前数据库服务器存在哪些数据库。就可以用得到如下命令。

语法：

```sql
SHOW DATABASES [LIKE '数据库名'];
```

示例：

```sql
; 查看全部数据库。
SHOW DATABASES;

; 搜索包含 test 的数据库。
SHOW DATABASES LIKE '%test%';

; 搜索以 test 开头的数据库。
SHOW DATABASES LIKE 'test%';

; 搜索以 test 结尾的数据库。
SHOW DATABASES LIKE '%test';
```



#### 1.5）选择数据库

当我们要对某一个数据库进行进一步的操作(创建表/编辑表)时。就会用到该命令。

语法：

```sql
USE <数据库名>
```

示例：

```sql
USE dbanme;
```



### 2）注释

在 `SQL` 编程中，难免会使用注释对 SQL 进行功能性说明或备注。

#### 2.1）单行注释

1) 单行注释可以使用 `#` 注释符，`#` 注释符后直接加注释内容。

格式如下：	

```
# 注释内容
```

单行注释使用注释符`#`的示例如下：

```sql
# 从结果中删除重复行
SELECT DISTINCT product_id, purchase_price FROM Product;
```



2) 单行注释可以使用 `--` 注释符，`--` 注释符后需要加一个空格，注释才能生效。

格式如下：

```sql
-- 注释内容
```

单行注释使用注释符 `--` 的示例如下：

```sql
-- 从结果中删除重复行
SELECT DISTINCT product_id, purchase_price FROM Product;
```

`#` 和 `--` 的区别就是：`#` 后面直接加注释内容，而 `--` 的第 2 个破折号后需要跟一个空格符在加注释内容。



#### 2.2）多行注释

多行注释使用 `/* */` 注释符。`/*` 用于注释内容的开头，`*/` 用于注释内容的结尾。

多行注释格式如下：

```sql
/*
  第一行注释内容
  第二行注释内容
*/
```

注释内容写在 `/* ` 和 `*/` 之间，可以跨多行。

多行注释的使用示例如下：

```sql
/*
这条 SELECT 语句,
会从结果中删除重复行
*/
SELECT DISTINCT product_id,purchase_price FROM Product;
```



> 任何注释（单行注释和多行注释）都可以插在 SQL 语句中，且注释可以放在 SQL 语句中的任意位置。
>
> 注释可以写在任何 SQL 语句当中，且 SQL 语句中对注释的数量没有限制。



### 3）表操作

在创建数据库之后，接下来就要在数据库中创建数据表。所谓创建数据表，指的是在已经创建的数据库中建立新表。

创建数据表的过程是规定数据列的属性的过程，同时也是实施数据完整性（包括实体完整性、引用完整性和域完整性）约束的过程。接下来我们介绍一下创建数据表的语法形式。



#### 3.1）语法

```sql
CREATE TABLE <表名> ([表定义选项])[表选项][分区选项];
```

其中，`[表定义选项]`的格式为：

```sql
<列名1> <类型1> [,…] <列名n> <类型n>
```



`CREATE TABLE` 命令语法比较多，其主要是由表创建定义（`create-definition`）、表选项（`table-options`）和分区选项（`partition-options`）所组成的。

这里首先描述一个简单的新建表的例子，然后重点介绍 `CREATE TABLE` 命令中的一些主要的语法知识点。

`CREATE TABLE` 语句的主要语法及使用说明如下：

- `CREATE TABLE`：用于创建给定名称的表，必须拥有表CREATE的权限。
- <表名>：指定要创建表的名称，在 `CREATE TABLE` 之后给出，必须符合标识符命名规则。表名称被指定为 `db_name.tbl_name`，以便在特定的数据库中创建表。无论是否有当前数据库，都可以通过这种方式创建。在当前数据库中创建表时，可以省略 `db-name`。如果使用加引号的识别名，则应对数据库和表名称分别加引号。例如，`'mydb'.'mytbl'` 是合法的，但 `'mydb.mytbl' `不合法。
- <表定义选项>：表创建定义，由列名（`col_name`）、列的定义（`column_definition`）以及可能的空值说明、完整性约束或表索引组成。
- 默认的情况是，表被创建到当前的数据库中。若表已存在、没有当前数据库或者数据库不存在，则会出现错误。



> 提示：使用 `CREATE TABLE` 创建表时，必须指定以下信息：
>
> - 要创建的表的名称不区分大小写，不能使用SQL语言中的关键字，如 `DROP`、`ALTER`、`INSERT` 等。
> - 数据表中每个列（字段）的名称和数据类型，如果创建多个列，要用逗号隔开。



#### 3.2）在指定的数据库中创建表

数据表属于数据库，在创建数据表之前，应使用语句“USE<数据库>”指定操作在哪个数据库中进行，如果没有选择数据库，就会抛出 `No database selected` 的错误。

【实例 1】创建员工表 `tb_emp1`，结构如下表所示。

| 字段名称 | 数据类型    | 备注         |
| -------- | ----------- | ------------ |
| id       | INT(ll)     | 员工编号     |
| name     | VARCHAR(25) | 员工名称     |
| deptld   | INT(ll)     | 所在部门编号 |
| salary   | FLOAT       | 工资         |



选择创建表的数据库 `test_db`，创建 `tb_emp1` 数据表，输入的 `SQL` 语句和运行结果如下所示。

```sql
mysql> USE test_db;
Database changed
mysql> CREATE TABLE tb_emp1
    -> (
    -> id INT(11),
    -> name VARCHAR(25),
    -> deptId INT(11),
    -> salary FLOAT
    -> );
Query OK, 0 rows affected (0.37 sec)
```

语句执行后，便创建了一个名称为 `tb_emp1` 的数据表，使用 `SHOW TABLES`；语句查看数据表是否创建成功，如下所示。

```sql
mysql> SHOW TABLES;
+--------------------+
| Tables_in_test_db  |
+--------------------+
| tb_emp1            |
+--------------------+
1 rows in set (0.00 sec)
```



> 我们在创建表的时候未指定表的存储引擎、以及字符集以及字符集校对规则。存储引擎会读取数据库配置中默认的存储引擎。高版本默认是 InnoDB 存储引擎。而字符集与字符集校对规则，会继承创建数据库时指定的配置。



**完整版**

```sql
CREATE TABLE tb_emp1(
    id int(11) UNSIGNED AUTO_INCREMENT COMMENT '员工ID。自增。',
    `name` varchar(25) NOT NULL COMMENT '员工姓名',
    deptId int(11) UNSIGNED NOT NULL DEFAULT '0' COMMENT '所在部门编号',
    salary FLOAT NOT NULL DEFAULT '0.00' COMMENT '工资',
		c_time datetime NOT NULL COMMENT '创建时间',
    primary key(id),
    key(`name`)
) ENGINE = InnoDB DEFAULT CHARSET utf8mb4 DEFAULT COLLATE utf8mb4_unicode_ci COMMENT '员工表';
```



- 指定的默认值。
- 指定了字段的说明。
- 指定了自增主键。
- 指定了普通索引。
- 指定了表存储引擎。
- 指定了字符集。
- 指定了字符集校对规则（排序规则）。
- 指定了表说明。



> 以上完整版就是我们平常创建一张表的常规操作。



#### 3.3）查看创建表时的 SQL

语法：

```sql
SHOW CREATE TABLE table_name;
```

示例：

```sql
mysql> show create table tb_emp1\G;
*************************** 1. row ***************************
       Table: tb_emp1
Create Table: CREATE TABLE `tb_emp1` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT COMMENT '员工ID。自增。',
  `name` varchar(25) COLLATE utf8mb4_unicode_ci NOT NULL COMMENT '员工姓名',
  `deptId` int(11) unsigned NOT NULL DEFAULT '0' COMMENT '所在部门编号',
  `salary` float NOT NULL DEFAULT '0' COMMENT '工资',
  `c_time` datetime NOT NULL COMMENT '创建时间',
  PRIMARY KEY (`id`),
  KEY `name` (`name`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci COMMENT='员工表'
1 row in set (0.00 sec)
```



#### 3.4）查看表结构

在 `MySQL` 中，使用 `SQL` 语句创建好数据表之后，可以查看结构的定义，以确认表的定义是否正确。在 `MySQL` 中，查看表结构可以使用 `DESCRIBE` 和 `SHOW CREATE TABLE` 语句。

`DESCRIBE/DESC` 语句可以查看表的字段信息，包括字段名、字段数据类型、是否为主键、是否有默认值等。

语法规则如下：

```sql
DESCRIBE <表名>;
```

或简写成：

```sql
DESC <表名>;
```

示例：

```sql
mysql> describe tb_emp1;
+--------+------------------+------+-----+---------+----------------+
| Field  | Type             | Null | Key | Default | Extra          |
+--------+------------------+------+-----+---------+----------------+
| id     | int(11) unsigned | NO   | PRI | NULL    | auto_increment |
| name   | varchar(25)      | NO   | MUL | NULL    |                |
| deptId | int(11) unsigned | NO   |     | 0       |                |
| salary | float            | NO   |     | 0       |                |
| c_time | datetime         | NO   |     | NULL    |                |
+--------+------------------+------+-----+---------+----------------+
5 rows in set (0.00 sec)
```



其中，各个字段的含义如下：

- `Null`：表示该列是否可以存储 `NULL` 值。
- `Key`：表示该列是否已编制索引。`PRI` 表示该列是表主键的一部分，`UNI` 表示该列是 `UNIQUE` 索引的一部分，`MUL` 表示在列中某个给定值允许出现多次。
- `Default`：表示该列是否有默认值，如果有，值是多少。
- `Extra`：表示可以获取的与给定列有关的附加信息，如 `AUTO_INCREMENT` 等。



#### 3.5）修改数据表

修改数据表的前提是数据库中已经存在该表。修改表指的是修改数据库中已经存在的数据表的结构。修改数据表的操作也是数据库管理中必不可少的。在 `MySQL ` 中可以使用 `ALTER TABLE` 语句来改变原有表的结构，例如增加或删减列、更改原有列类型、重新命名列或表等。

语法：

```sql
ALTER TABLE <表名> [修改选项]
```

修改选项的语法格式如下：

```sql
{ ADD COLUMN <列名> <类型>
| CHANGE COLUMN <旧列名> <新列名> <新列类型>
| ALTER COLUMN <列名> { SET DEFAULT <默认值> | DROP DEFAULT }
| MODIFY COLUMN <列名> <类型>
| DROP COLUMN <列名>
| RENAME TO <新表名>
| CHARACTER SET <字符集名>
| COLLATE <校对规则名> }
```

**修改表名：**

```sql
ALTER TABLE tb_emp1 RENAME TO tb_emp1_new;
```

**修改字符集：**

```sql
ALTER TABLE tb_emp1 CHARACTER SET gb2312 DEFAULT COLLATE gb2312_chinese_ci;
```



#### 3.6）修改数据表字段

语法：

```sql
ALTER TABLE <表名> CHANGE <旧字段名> <新字段名> <新数据类型>;
```

其中：

- 旧字段名：指修改前的字段名；
- 新字段名：指修改后的字段名；
- 新数据类型：指修改后的数据类型，如果不需要修改字段的数据类型，可以将新数据类型设置成与原来一样，但数据类型不能为空。



示例：

```sql
ALTER TABLE tb_emp1 CHANGE name new_name VARCHAR(50) NOT NULL COMMENT '新员工姓名字段';
```



**改字段类型：**

修改字段的数据类型就是把字段的数据类型转换成另一种数据类型。在 MySQL 中修改字段数据类型的语法规则如下：

```sql
ALTER TABLE <表名> MODIFY <字段名> <数据类型>
```

其中：

- 表名：指要修改数据类型的字段所在表的名称；-
- 字段名：指需要修改的字段；
- 数据类型：指修改后字段的新数据类型。



示例：

```sql
ALTER TABLE tb_emp1 MODIFY name VARCHAR(20) NOT NULL COMMENT '字段名称修改';
```



**删除字段：**

删除字段是将数据表中的某个字段从表中移除，语法格式如下：

```sql
ALTER TABLE <表名> DROP <字段名>;
```



示例：

```sql
ALTER TABLE tb_emp1 DROP name;
```



#### 3.7）添加数据表字段

一个完整的字段包括字段名、数据类型和约束条件。MySQL 添加字段的语法格式如下：

```sql
ALTER TABLE <表名> ADD <新字段名><数据类型>[约束条件];
```

对语法格式的说明如下：                    

- <表名> 为数据表的名字；
- <新字段名> 为所要添加的字段的名字；
- <数据类型> 为所要添加的字段能存储数据的数据类型；
- [约束条件] 是可选的，用来对添加的字段进行约束。

> 这种语法格式默认在表的最后位置（最后一列的后面）添加新字段。



示例：

```sql
ALTER TABLE tb_emp1 ADD name varchar(20) NOT NULL COMMENT '员工姓名';
```



默认情况下添加的新字段是在最后。如果想在第一前面添加。可以如下：

```sql
ALTER TABLE tb_emp1 ADD name varchar(20) NOT NULL COMMENT '员工姓名' FIRST;
```

我们在最后增加了一个 `FIRST` 。



**如果想在指定字段前面增加新字段：**

语法：

```sql
ALTER TABLE <表名> ADD <新字段名> <数据类型> [约束条件] AFTER <已经存在的字段名>;
```

示例：

```sql
ALTER TABLE tb_emp1 ADD name varchar(20) NOT NULL COMMENT '员工姓名' AFTER id;
```



#### 3.8）删除数据表

在删除表的同时，表的结构和表中所有的数据都会被删除，因此在删除数据表之前最好先备份，以免造成无法挽回的损失。



使用 **DROP TABLE** 语句可以删除一个或多个数据表，语法格式如下：

```sql
DROP TABLE [IF EXISTS] 表名1 [ ,表名2, 表名3 ...]
```



对语法格式的说明如下：

- `表名1, 表名2, 表名3 ...`表示要被删除的数据表的名称。`DROP TABLE` 可以同时删除多个表，只要将表名依次写在后面，相互之间用逗号隔开即可。
- `IF EXISTS` 用于在删除数据表之前判断该表是否存在。如果不加 `IF EXISTS`，当数据表不存在时 `MySQL` 将提示错误，中断 `SQL` 语句的执行；加上 `IF EXISTS` 后，当数据表不存在时 `SQL` 语句可以顺利执行，但是会发出警告`（warning）`。



两点注意：

- 用户必须拥有执行 `DROP TABLE` 命令的权限，否则数据表不会被删除。
- 表被删除时，用户在该表上的权限不会自动删除。



示例 1（删除一个表）：

```sql
DROP TABLE tb_emp1;
```



示例 2（删除多个表）：

```sql
DROP TABLE tb_emp1,tb_emp2,tb_emp3;
```



为了避免表不存在导致批量删除多个表出现报错，以后后续表的删除失败。我们可以如下操作：

```sql
DROP TABLE IF EXISTS tb_emp1,tb_emp2,tb_emp3;
```



### 4）目录结构介绍

`MySQL` 安装完成后，会在磁盘上生成一个目录，该目录就被称为 `MySQL` 的安装目录。

`MySQL` 的安装目录包含一些子目录以及一些后缀名为 `.ini` 的配置文件。为了更好地学习 `MySQL`，初学者必须要对 `MySQL` 安装目录下各个子目录的意义和作用有所了解。下面对 `MySQL` 的安装目录进行详细讲解。 

目录结果如下：

```
├──  bin
├──  data
├──  docs
├──  include
├──  lib
├──  lib64 -> lib
├──  man
├──  mysql-test
├──  scripts
├──  share
├──  sql-bench
└──  support-files
```

#### 1）bin 目录

用于放置一些可执行文件，如 `mysql`、`mysqld`、`mysqlshow` 、`mysqladmin`等。

#### 2）docs 目录

存放一些文档。

#### 3）data 目录

登录数据库后，可使用 `SHOW GLOBAL VARIABLES LIKE "%Datadir%";` 命令查看 Data 目录位置。

`data` 目录中用于放置一些日志文件以及数据库。我们创建和保存的数据都存在这个目录里。打开 `data 目录`，结构如下图所示：

```
.
├── mysql
├── performance_schema
├── safe@002dtoken
├── test
├── yaf@002dserver@002dadmin
└── yafdb
```

我们查看一下 `test ` 数据库的目录结果：

```
.
├── db.opt
├── shuru.frm
├── shuru.ibd
├── tb_emp1.frm
├── tb_emp1.ibd
├── tb_event.frm
├── tb_event.ibd
├── tb_lock.frm
├── tb_lock.ibd
├── tb_logs.frm
├── tb_logs.ibd
├── tb_money.frm
├── tb_money.ibd
├── tb_sms.frm
├── tb_sms.ibd
├── tb_user.frm
└── tb_user.ibd
```



在该数据库目录中，因为我的表是 `InnoDb` 存储引擎。所以，每张表都有两个文件。`.frm`  是描述表结构的文件。`.idb` 是 `innodb` 存储引擎表的数据文件。如果是 `MyISAM` 存储引擎，则表文件后缀又不一样。比如 `.MYD` 是 `MyISAM` 存储引擎对应生成的存储表数据的文件。`.MYI` 是 `MyISAM` 存储引擎对应生成的索引文件。



> `db.opt` 文件。眼尖的朋友应该发现了这个文件。它是数据库创建时自动生成的文件。它是一个普通的文本文件。打开之后，里面记录的是数据库字符集以及字符集校对规则(排序规则)。



#### 4）include 目录

用于放置一些头文件，如：`mysql.h`、`mysql_ername.h` 等。



#### 5）lib 目录

用于放置一系列库文件。



#### 6）share 目录

用于存放字符集、语言等信息。



#### 7）my.cnf文件

`my.cnf` 是 `MySQL` 默认使用的配置文件，一般情况下，只要修改 `my.cnf` 配置文件中的内容就可以对 `MySQL` 进行配置。

> 在 `windows` 系统，配置文件名称是：`my.ini`。



### 5）查看服务器配置

通常我们不会在 `my.cnf` 配置文件之中把所有的 `mysql` 配置列举出来一一设置。只会对关键的几个我们需要的配置进行设置。其他全部采用 `mysql` 的默认配置。

那么，此时我们要查看所有的配置该怎么办呢？

通过如下命令：

``` sql
SHOW VARIABLES;
```

关于 `mysql` 的配置有很多。这里不展开细讲。后面，我们会单独开设一章节进行讲解。这里仅告知如何查看。



由于这个配置实在太多。假如我们只想查询某个配置该怎么办呢?

```sql
mysql> SHOW VARIABLES LIKE "max_connections";
+-----------------+-------+
| Variable_name   | Value |
+-----------------+-------+
| max_connections | 151   |
+-----------------+-------+
1 row in set (0.00 sec)
```



也可以：

```sql
mysql> SHOW VARIABLES LIKE "%connection%";
+--------------------------+-----------------+
| Variable_name            | Value           |
+--------------------------+-----------------+
| character_set_connection | utf8            |
| collation_connection     | utf8_general_ci |
| max_connections          | 151             |
| max_user_connections     | 0               |
+--------------------------+-----------------+
4 rows in set (0.00 sec)
```

如此这般，就可以查看包含 `connection` 关键字的配置了。





















