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



##### 1.2.1) 更改数据库名称

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

















