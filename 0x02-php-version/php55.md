# 0x02.3 PHP 5.5 版本特性

`PHP 5.5 版本可以说是一个非常重大的版本。它增加了很多的特性。只有了解这些重要的特性，才能避免使用已弃用的特性与方法，或使用即将弃用的方法导致未来版本升级带来的风险。其次，新增的方法或参数可能会让自己的代码少写一些代码提升性能与维护。

> 关于 `PHP 5.5` 完整的弃用、移除、新增等特性。官方文档：https://www.php.net/manual/zh/migration55.php

下面我们会挑核心重点进行说明。面试的时候也算是重点考察的范围。



### 1）新增的函数

```
array_column() - 返回数组中指定的一列。这个函数非常常用。在之前版本没有的时候只能自己实现。
boolval() - 转换变布尔类型。也挺实用。
password_get_info() - 返回指定散列（hash）的相关信息。
password_hash() - 创建密码的散列（hash）。
password_needs_rehash() - 检测散列值是否匹配指定的选项。
password_verify() - 验证密码是否和散列值匹配。
```

> 更多的其他扩展新增的函数：https://www.php.net/manual/zh/migration55.new-functions.php



### 2）`mysql` 扩展被废弃

这应该算是 `PHP` 的一个重大事件了吧。很多应用或者开发者还在使用 `mysql_` 开头的函数族来与数据库打交道。所以，从 `PHP 5.5` 之后，就能再使用了。

可使用 [MySQLi](https://www.php.net/manual/zh/book.mysqli.php) 或 [PDO_MySQL](https://www.php.net/manual/zh/ref.pdo-mysql.php) 扩展作为替代。



### 3）新增生成器`Generator`

这应该也算是 `PHP` 的一个重点特性了。

生成器提供了一种更容易的方法来实现简单的对象迭代。相比较定义类实现 **Iterator** 接口的方式，性能开销和复杂性大大降低。

生成器文档：https://www.php.net/manual/zh/language.generators.overview.php



### 4）`preg_replace()` 中的 `/e` 修饰符被弃用

[preg_replace()](https://www.php.net/manual/zh/function.preg-replace.php) 函数中用到的 */e* 修饰符现在被弃用。可以使用 [preg_replace_callback()](https://www.php.net/manual/zh/function.preg-replace-callback.php) 函数来替代。



### 5）新增 `finally` 关键字

可以说这个 `finally` 就非常关键了。异常的时候，通过 `finally` 可以处理一些特殊的工作。

```php
<?php
try {
    echo inverse(5) . "\n";
} catch (Exception $e) {
    echo 'Caught exception: ',  $e->getMessage(), "\n";
} finally {
    echo "First finally.\n";
}
```



### 6）`foreach` 支持 `list()`

[foreach](https://www.php.net/manual/zh/control-structures.foreach.php) 控制结构现在支持通过 [list()](https://www.php.net/manual/zh/function.list.php) 构造将嵌套数组分离到单独的变量。

```php
<?php
$array = [
    [1, 2],
    [3, 4],
];

foreach ($array as list($a, $b)) {
    echo "A: $a; B: $b\n";
}
```

以上示例会输出：

```
A: 1; B: 2
A: 3; B: 4
```



### 7）`empty()` 支持任意表达式

`empty()` 现在支持传入一个任意表达式，而不仅是一个变量。

```php
<?php
function always_false() {
    return false;
}

if (empty(always_false())) {
    echo 'This will be printed.';
}

if (empty(true)) {
    echo 'This will not be printed.';
}
```

输出：

```
This will be printed.
```



### 8）array and string literal dereferencing

这个用中文还真没有啥好的方式。通俗的讲就是数组和字符串可以直接定义的时候直接访问里面的元素了。

```php
<?php
echo 'Array dereferencing: ';
echo [1, 2, 3][0];
echo "\n";

echo 'String dereferencing: ';
echo 'PHP'[0];
echo "\n";
```

输出：

```
Array dereferencing: 1
String dereferencing: P
```