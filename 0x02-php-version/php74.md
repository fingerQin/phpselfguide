# 0x02.7 PHP 7.4 版本特性

关于 `PHP 7.4` 的变更，可以查看官方文档：https://www.php.net/manual/zh/migration74.php



### 1）类属性支持类型限定

这个限定变得非常有意义。

```php
<?php
class User {
    public int $id;
    public string $name;
}
```



### 2）Arrow functions

[Arrow functions](https://www.php.net/manual/zh/functions.arrow.php) provide a shorthand syntax for defining functions with implicit by-value scope binding.

```php
<?php
$factor = 10;
$nums = array_map(fn($n) => $n * $factor, [1, 2, 3, 4]);
// $nums = array(10, 20, 30, 40);
```