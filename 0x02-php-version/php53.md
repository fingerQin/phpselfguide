# 0x02.1 PHP 5.3 版本特性

`PHP 5.3` 版本可以说是一个非常重大的版本。它增加了很多的特性。只有了解这些重要的特性，才能避免使用已弃用的特性与方法，或使用即将弃用的方法导致未来版本升级带来的风险。其次，新增的方法或参数可能会让自己的代码少写一些代码提升性能与维护。

> 关于 `PHP 5.3` 完整的弃用、移除、新增等特性。官方文档：https://www.php.net/manual/zh/migration53.php

下面我们会挑核心重点进行说明。面试的时候也算是重点考察的范围。



### 1）支持命名空间（`Namespace`）

几乎所有的面向对象的语言都有命名空间的支持。毫无疑问，命名空间是 `PHP 5.3` 所带来的最重要的新特性。在之前的版本之中，遇到同名类的时候处理起来就非常的麻烦。现在有了命名空间之后，可以很轻松通过命名空间隔离，并在 `use` 的时候通过别名来区别。避免同名冲突。代码简洁易读易维护了。

准确说该特性是从 `PHP 5.3.0` 版本开始支持。

> 关于命名空间官网文档在此：https://www.php.net/manual/zh/language.namespaces.php



### 2）支持静态延迟绑定

自 `PHP 5.3.0` 开始，`PHP` 增加了一个叫做后期静态绑定的功能，用于在继承范围内引用静态调用的类。可以简单理解为：当子类继承了父类的方法，父类方法中调用的 `static::` 运算符代表的不是父类。而是子类。

毕竟在运行的时候，不知道是哪个子类调用了该方法。所以，只有在运行的时候才能确定这个 `static::` 代表是哪个类。所以，称之为静态延迟绑定。

```
self:: 代表当前类。
parent:: 代表父类。
static:: 代表实际调用该方法的类。
```

`static::` 调用时，即使该方法是在父类定义，在子类使用。但是，此时的 `static::` 代表的是子类。因为此时是当前调用的类是子类。

示例：

```php
<?php
class A {
    public static function who() {
        echo __CLASS__;
    }
    public static function test() {
        static::who(); // 后期静态绑定从这里开始
    }
}

class B extends A {
    public static function who() {
        echo __CLASS__;
    }
}

B::test();
?>
```

以上示例会输出：

```
B
```

虽然在父类中调用了子类定义的方法。由于 `static::` 代表了是当前调用的类。所以，它代表类 `B`。

> 更多静态延迟绑定请阅读官网文档：https://www.php.net/manual/zh/language.oop5.late-static-bindings.php



### 3）支持 `goto` 语句

自 `PHP 5.3.0` 开始支持 `goto` 语句。并且不能再使用 `goto` 用作函数名类名等。已经是一个保留字。学过 `C` 语言的人应该知道这个 `goto` 语句的功能。

还记得在大学的时候，笔者的老师说一个优秀的代码就是尽量不要使用 `goto` 语句来跳转代码块。它会使代码难以阅读。所以，即使 `PHP` 支持 `goto` 语句，也不要滥用噢。

> 关于 `goto` 语句的使用介绍可阅读官网文档：https://www.php.net/manual/zh/control-structures.goto.php



### 4）支持原生的闭包(Lambda/匿名函数)

匿名函数（Anonymous functions），也叫闭包函数（*closures*），允许 临时创建一个没有指定名称的函数。最经常用作回调函数（callback）参数的值。当然，也有其它应用的情况。

> 关于闭包请阅读官网文档：https://www.php.net/manual/zh/functions.anonymous.php





### 5）新增了两个魔术方法, `__callStatic`() 和 `__invoke()`

截止目前 `PHP` 已经为类提供了 15 个魔术方法。而在 `PHP 5.3.0` 的时候增加了 `__callStatic()` 和 `__invoke()` 两个魔术方法。

> `PHP` 会把所有以 `__` （两个下划线）开头的类方法保留为魔术方法。所以在定义类方法时，建议不要以 `__` 为前缀。

- `__callStatic()` - 在访问类中一个不存在的静态方法时会自动调用该方法。
- `__invoke()` - 当尝试以调用函数的方法调用一个对象时，该方法会自动被调用。

> 文档：
>
> `__callStatic()` - https://www.php.net/manual/zh/language.oop5.overloading.php#object.callstatic
>
> `__invoke()` - https://www.php.net/manual/zh/language.oop5.magic.php#object.invoke



### 6）新增  `Nowdoc` 语法

使用过 `Heredoc` 结构的人应该很容易理解 `Nowdoc` 结构。它们之前最明显的区别：`Heredoc` 的包含的字符串中的变量是可以被解析，而 `Nowdoc` 是不会解析的。

它们就像我们常用的字符串一样：`Heredoc` 相当于是双引号包含的字符串，`Nowdoc` 相当于是单引号包含的字符串。

> 官方文档：https://www.php.net/manual/zh/language.types.string.php#language.types.string.syntax.nowdoc



### 7）可以在类外部使用 `const` 关键词声明常量

没有这个特性之前，只能使用 `define()` 来定义常量。现在可以使用 `const` 来定义常量了。

> 和使用 define() 来定义常量相反的是，使用 *const* 关键字定义常量必须处于最顶端的作用区域，因为用此方法是在编译时定义的。这就意味着不能在函数内，循环内以及 *if* 语句之内用 *const* 来定义常量。



### 8）三元运算操作符增加了简写形式`?:`

常用写法：`(expr1) ? (expr2) : (expr3)`

简写写法：`expr1 ?: expr3 `



### 9）HTTP 流包裹器将从 200 到 399 全部的状态码都视为成功



### 10）支持动态访问静态方法

```php
<?php
class Test{    
    public static function testgo()    
    {    
         echo "gogo!";    
    }    
}    
$class  = 'Test';    
$action = 'testgo';    
$class::$action();  //输出 "gogo!"   
```



### 11）异常可以被内嵌

试想一下，如果异常不能内嵌（嵌套），那异常的变会变得非常鸡肋。因为，你不知道被调用的代码内是否已使用异常。如果在外层再使用异常就非常麻烦。底层代码不敢轻易使用，上层代码也不敢轻易使用。确实麻烦。

> 官方文档：https://www.php.net/manual/zh/language.exceptions.php

```php
<?php

class MyException extends Exception { }

class Test {
    public function testing() {
        try {
            try {
                throw new MyException('foo!');
            } catch (MyException $e) {
                // rethrow it
                throw $e;
            }
        } catch (Exception $e) {
            var_dump($e->getMessage());
        }
    }
}

$foo = new Test;
$foo->testing();
```



### 12）新增了循环引用的垃圾回收器并且默认是开启的



### 13）mail() 现在支持邮件发送日志。

> 注意: 仅支持通过该函数发送的邮件。



### 14）弃用函数

```
dl() - 运行时载入一个 PHP 扩展。在某些 SAPI 中被移除。
spliti() - 用正则表达式不区分大小写将字符串分割到数组中。PHP 5.3.0 弃用。
split() - PHP 5.3.0 弃用，PHP 7 移除。可使用 preg_split()、str_split() 替代。
set_socket_blocking() - PHP 5.3.0 弃用，PHP 7 移除。使用 stream_set_blocking() 替代。
session_is_registered() - 验证 SESSION 是否存在某个键。PHP 5.3.0 弃用，PHP 5.4.0 移除。
session_unregister() - 删除 SESSION 中一个键。PHP 5.3.0 弃用，PHP 5.4.0 移除。
set_magic_quotes_runtime() - PHP 5.3.0 弃用，PHP 7 移除。
```



### 15）弃用的 `INI` 指令

```
define_syslog_variables() - PHP >= 5.4.0 以上移除不可使用。
register_globals - PHP 5.3.0 废弃并在 PHP 5.4.0 移除。
register_long_arrays - PHP 5.3.0 弃用。PHP 5.4.0 移除。
safe_mode - PHP 安全模式从 PHP 5.3.0 起废弃并在 PHP 5.4.0 移除。
magic_quotes_gpc - 魔术引号已在 PHP 5.3.0 中去除。原本的用意是自动转义避免攻击。
magic_quotes_runtime - 对执行 SQL 查询的结果执行 addslashes() 转义。PHP 5.3.0 废除。PHP 7 移除。
magic_quotes_sybase - 已在 PHP 5.3.0 中弃用，PHP 5.4.0 移除。
```

> 关于 `INI` 指令可以在官网文档查阅：https://www.php.net/manual/zh/ini.core.php 上面只列举了最关键重要的一部分。大家可以在该文档中搜索 `5.3` 关键词。可以获息哪些指令被弃用，哪些被移除，哪些是新增。



> 关于弃用的函数或 `INI` 完整文档：https://www.php.net/manual/zh/migration53.deprecated.php



### 16）新增两个错误等级

`PHP 5.3.0` 新增了两个错误等级: **`E_DEPRECATED`** 和 **`E_USER_DEPRECATED`**. 错误等级 **`E_DEPRECATED`** 被用来说明一个函数或者功能已经被弃用. **`E_USER_DEPRECATED`** 等级目的在于表明用户代码中的弃用功能, 类似于 **`E_USER_ERROR`** 和 **`E_USER_WARNING`** 等级。



### 17）新增 `__DIR__` 常量

这个魔术常量用得非常广泛。它可是在 `PHP 5.3.0` 增加的。可别在低版本使用。



### 18）新增 `__NAMESPACE__` 常量

由于 `PHP 5.3.0` 增加了命名空间的特性。所以，这个魔术常量是针对此特性新增的。获取当前命名空间名称。



### 19）`mysqlnd` 驱动内置

从 `PHP 5.3.0`开始，`mysqlnd` 已是 `PHP` 内置的驱动。之前的版本要使用 `mysqlnd` 需要单独安装。



### 20）新增的方法

```
parse_ini_string() - 解析配置字符串。
array_replace() - 使用传递的数组替换第一个数组的元素。
array_replace_recursive() - 使用传递的数组递归替换第一个数组的元素。
class_alias() - 为一个类创建别名。
lcfirst() - 使一个字符串的第一个字符小写。
str_getcsv() - 解析 CSV 字符串为一个数组。
```

> 更多：https://www.php.net/manual/zh/migration53.functions.php