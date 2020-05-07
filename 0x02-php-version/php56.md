# 0x02.4 PHP 5.6 版本特性

`PHP 5.6` 版本可以说是一个非常重大的版本。它增加了很多的特性。只有了解这些重要的特性，才能避免使用已弃用的特性与方法，或使用即将弃用的方法导致未来版本升级带来的风险。其次，新增的方法或参数可能会让自己的代码少写一些代码提升性能与维护。

> 关于 `PHP 5.6` 完整的弃用、移除、新增等特性。官方文档：https://www.php.net/manual/zh/migration56.php

下面我们会挑核心重点进行说明。面试的时候也算是重点考察的范围。



### 1）类常量可以通过 `const` 定义数组

```php
<?php
const ARR = ['a', 'b'];

echo ARR[0];
```

输出：

```
a
```



### 2）使用 `...` 运算符定义变长参数函数

可以说这个特性变得非常实用了。很多其他语言也具备类似的特性。

```php
<?php
function f($req, $opt = null, ...$params) {
    // $params 是一个包含了剩余参数的数组
    printf('$req: %d; $opt: %d; number of params: %d'."\n",
           $req, $opt, count($params));
}

f(1);
f(1, 2);
f(1, 2, 3);
f(1, 2, 3, 4);
f(1, 2, 3, 4, 5);
```



### 3）使用 `...`  运算符进行参数展开

在调用函数的时候，使用 `...` 运算符，将数组和可遍历对象展开为函数参数。 在其他编程语言，比如 `Ruby` 中，这被称为连接运算符。

```php
<?php
function add($a, $b, $c) {
    return $a + $b + $c;
}

$operators = [2, 3];
echo add(1, ...$operators);
```

输出：

```
6
```



### 4）使用 `**` 进行幂运算

加入右连接运算符 `**` 来进行幂运算。 同时还支持简写的 `**=` 运算符，表示进行幂运算并赋值。

```php
<?php
printf("2 ** 3 ==      %d\n", 2 ** 3);
printf("2 ** 3 ** 2 == %d\n", 2 ** 3 ** 2);

$a = 2;
$a **= 3;
printf("a ==           %d\n", $a);
```

以上程序输出：

```
2 ** 3 ==      8
2 ** 3 ** 2 == 512
a ==           8
```



### 5）`use function` 以及 `use const`

[*use*](https://www.php.net/manual/zh/language.namespaces.importing.php) 运算符 被进行了扩展以支持在类中导入外部的函数和常量。 对应的结构为 `use function` 和 `use const`。

```php
<?php
namespace Name\Space {
    const FOO = 42;
    function f() { echo __FUNCTION__."\n"; }
}

namespace {
    use const Name\Space\FOO;
    use function Name\Space\f;

    echo FOO."\n";
    f();
}
```

输出：

```
42
Name\Space\f
```



### 6）`phpdbg`

`PHP` 的 `SAPI` 模块中实现了一个 交互式调试器，叫做 `phpdbg`。更多信息，请访问 [phpdbg documentation](https://www.php.net/manual/zh/book.phpdbg.php)。



### 7）`php://input` 是可重用的了

只要你需要，你可以多次打开并读取 `php://input`。 同时，这个特性使得在处理 `POST` 的数据的时候， 可以明显降低对于内存的需求量。



### 8）大文件上传

现在可以支持大于 `2GB` 的文件上传。

