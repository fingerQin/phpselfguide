# 0x02.5 PHP 7.2 版本特性

本小节会整合 `PHP 7.0`、`PHP 7.1`、`PHP 7.2` 三个版本的特性。因为，现在推荐使用的最低 `PHP 7` 版本为 `PHP 7.2`。



`PHP 7.0` 变更文档：https://www.php.net/manual/zh/migration70.php

`PHP 7.1` 变更文档：https://www.php.net/manual/zh/migration71.php

`PHP 7.2` 变更文档：https://www.php.net/manual/zh/migration72.php



### 1）标题类型声明

这是 `PHP 7` 里面最重大的改变。简单点说，就是限制方法/函数的参数类型以及返回类型。如果不符合这个类型就会报错。

而标题类型的声明又有两种模式：强制(默认) 和 严格模式。

适应的标题类型：`stinrg`、`array`、`int`、`float`、`bool`、类名、接口、回调类型。

```php
<?php
// Coercive mode
function sumOfInts(int ...$ints)
{
    return array_sum($ints);
}

var_dump(sumOfInts(2, '3', 4.1));
```

以上程序输出：

```
9
```

如果调用的时候传入的参数类型与之不匹配就会报错。



### 2）强制限制返回类型

`PHP 7` 增加了对[返回类型声明](https://www.php.net/manual/zh/functions.returning-values.php#functions.returning-values.type-declaration)的支持。 类似于[参数类型声明](https://www.php.net/manual/zh/functions.arguments.php#functions.arguments.type-declaration)，返回类型声明指明了函数返回值的类型。可用的[类型](https://www.php.net/manual/zh/functions.arguments.php#functions.arguments.type-declaration.types)与参数声明中可用的类型相同。

```php
<?php

function arraysSum(array ...$arrays): array
{
    return array_map(function(array $array): int {
        return array_sum($array);
    }, $arrays);
}

print_r(arraysSum([1,2,3], [4,5,6], [7,8,9]));
```

以上程序输出：

```
Array
(
    [0] => 6
    [1] => 15
    [2] => 24
)
```



### 3）null 合并运算符

由于日常使用中存在大量同时使用三元表达式和 `isset()` 的情况， 我们添加了null合并运算符` (??)` 这个语法糖。如果变量存在且值不为**`NULL`**， 它就会返回自身的值，否则返回它的第二个操作数。

```php
<?php
// Fetches the value of $_GET['user'] and returns 'nobody'
// if it does not exist.
$username = $_GET['user'] ?? 'nobody';
// This is equivalent to:
$username = isset($_GET['user']) ? $_GET['user'] : 'nobody';

// Coalesces can be chained: this will return the first
// defined value out of $_GET['user'], $_POST['user'], and
// 'nobody'.
$username = $_GET['user'] ?? $_POST['user'] ?? 'nobody';
```



### 4）太空船操作符（组合比较符） 

太空船操作符用于比较两个表达式。当$a小于、等于或大于$b时它分别返回-1、0或1。 比较的原则是沿用 PHP 的[常规比较规则](https://www.php.net/manual/zh/types.comparisons.php)进行的。

```php
<?php
// 整数
echo 1 <=> 1; // 0
echo 1 <=> 2; // -1
echo 2 <=> 1; // 1

// 浮点数
echo 1.5 <=> 1.5; // 0
echo 1.5 <=> 2.5; // -1
echo 2.5 <=> 1.5; // 1
 
// 字符串
echo "a" <=> "a"; // 0
echo "a" <=> "b"; // -1
echo "b" <=> "a"; // 1
```



### 5）通过 `define()` 定义常量数组

`Array` 类型的常量现在可以通过 `define()` 来定义。在 `PHP5.6` 中仅能通过 `const` 定义。

```php
<?php
define('ANIMALS', [
    'dog',
    'cat',
    'bird'
]);

echo ANIMALS[1]; // 输出 "cat"
```



### 6）匿名类

现在支持通过 `new class` 来实例化一个匿名类，这可以用来替代一些“用后即焚”的完整类定义。

```php
<?php
interface Logger {
    public function log(string $msg);
}

class Application {
    private $logger;

    public function getLogger(): Logger {
         return $this->logger;
    }

    public function setLogger(Logger $logger) {
         $this->logger = $logger;
    }
}

$app = new Application;
$app->setLogger(new class implements Logger {
    public function log(string $msg) {
        echo $msg;
    }
});

var_dump($app->getLogger());
```

以上程序输出：

```
object(class@anonymous)#2 (0) {
}
```

> 匿名类文档：https://www.php.net/manual/zh/language.oop5.anonymous.php



### 7）Closure::call() 

**Closure::call()** 现在有着更好的性能，简短干练的暂时绑定一个方法到对象上闭包并调用它。

```php
<?php
class A {private $x = 1;}

// PHP 7 之前版本的代码
$getXCB = function() {return $this->x;};
$getX = $getXCB->bindTo(new A, 'A'); // 中间层闭包
echo $getX();

// PHP 7+ 及更高版本的代码
$getX = function() {return $this->x;};
echo $getX->call(new A);
```

以上输出：

```
1
1
```



### 8）Group *use* declarations 

从同一 [*namespace*](https://www.php.net/manual/zh/language.namespaces.definition.php) 导入的类、函数和常量现在可以通过单个 [*use*](https://www.php.net/manual/zh/language.namespaces.importing.php) 语句 一次性导入了。

```php
<?php

// PHP 7 之前的代码
use some\namespace\ClassA;
use some\namespace\ClassB;
use some\namespace\ClassC as C;

use function some\namespace\fn_a;
use function some\namespace\fn_b;
use function some\namespace\fn_c;

use const some\namespace\ConstA;
use const some\namespace\ConstB;
use const some\namespace\ConstC;

// PHP 7+ 及更高版本的代码
use some\namespace\{ClassA, ClassB, ClassC as C};
use function some\namespace\{fn_a, fn_b, fn_c};
use const some\namespace\{ConstA, ConstB, ConstC};
```



### 9）生成器可以返回表达式

此特性基于 `PHP 5.5` 版本中引入的生成器特性构建的。 它允许在生成器函数中通过使用 `return` 语法来返回一个表达式 （但是不允许返回引用值）， 可以通过调用 `Generator::getReturn()` 方法来获取生成器的返回值， 但是这个方法只能在生成器完成产生工作以后调用一次。

```php
<?php

$gen = (function() {
    yield 1;
    yield 2;

    return 3;
})();

foreach ($gen as $val) {
    echo $val, PHP_EOL;
}

echo $gen->getReturn(), PHP_EOL;
```

以上程序输出：

```
1
2
3
```

在生成器中能够返回最终的值是一个非常便利的特性， 因为它使得调用生成器的客户端代码可以直接得到生成器（或者其他协同计算）的返回值， 相对于之前版本中客户端代码必须先检查生成器是否产生了最终的值然后再进行响应处理 来得方便多了。



### 10）整数除法函数 `intdiv()`

新加的函数 [intdiv()](https://www.php.net/manual/zh/function.intdiv.php) 用来进行 整数的除法运算。

```php
<?php

var_dump(intdiv(10, 3));
```

以上程序输出：

```
int(3)
```



### 11）弃用 `PHP4` 风格的构造函数

`PHP4` 风格的构造函数（方法名和类名一样）将被弃用，并在将来移除。 如果在类中仅使用了 `PHP4` 风格的构造函数，`PHP7` 会产生 **`E_DEPRECATED`** 警告。 如果还定义了 **__construct()** 方法则不受影响。

```php
<?php
class foo {
    function foo() {
        echo 'I am the constructor';
    }
}
```



### 12）静态调用非静态的方法

废弃了 [静态（Static）](https://www.php.net/manual/zh/language.oop5.static.php) 调用未声明成 **static** 的方法，未来可能会彻底移除该功能。

```php
<?php
class foo {
    function bar() {
        echo 'I am not static!';
    }
}

foo::bar();
```

以上方式将不被允许。



### 13）`dirname()` 增加第二个参数

在之前如果我们取得上两级的目录路径，只能两次操作。

如：

```php
<?php
$dirPath = dirname(dirname(__DIR__));
```

现在增加第二个参数之后：

```php
<?php
$dirPath = dirname(__DIR__, 2);
```

相较以前代码更简洁、更易读理解。



### 14）`PHP 7.0` 新增函数

```
random_bytes()
random_int()
error_clear_last()
intdiv()
```

完整新增函数列表：https://www.php.net/manual/zh/migration70.new-functions.php



### 15）`FPM listen` 端口同时监听 `IPv4` 和 `IPv6`

在 `PHP 5` 中，[listen](https://www.php.net/manual/zh/install.fpm.configuration.php#listen) 指令如果仅带一个端口数字， 则会监听所有网络接口，但只是 `IPv4`。 现在 `PHP 7` 会同时接受来自 `IPv4` 和 `IPv6` 上的请求。

指定了 `ip` 地址后不受此影响；它们会继续仅仅监听在指定的地址和协议上。



### 16）移除的扩展

```
ereg
mssql
mysql
sybase_ct
```



### 17）放宽了保留词的限制

现在允许全局保留词用于类/接口/`Trait` 中的属性、常量和方法名。 在引入新关键词时，此变更减少了对向后兼容的破坏，避免了 `API` 命名的限制。



### 18）可为空（`Nullable`）类型

`PHP 7.1`：参数以及返回值的类型现在可以通过在类型前加上一个问号使之允许为空。 当启用这个特性时，传入的参数或者函数返回的结果要么是给定的类型，要么是 `null`。

```php
<?php

function testReturn(): ?string
{
    return 'elePHPant';
}

var_dump(testReturn());

function testReturn(): ?string
{
    return null;
}

var_dump(testReturn());

function test(?string $name)
{
    var_dump($name);
}

test('elePHPant');
test(null);
test();
```

以上程序输出：

```
string(10) "elePHPant"
NULL
string(10) "elePHPant"
NULL
Uncaught Error: Too few arguments to function test(), 0 passed in...
```



### 19）void 函数

`PHP 7.1` 一个新的返回值类型 `void` 被引入。 返回值声明为 `void` 类型的方法要么干脆省去`return` 语句，要么使用一个空的 `return` 语句。 对于 `void` 函数来说，`NULL` 不是一个合法的返回值。

```php
<?php
function swap(&$left, &$right) : void
{
    if ($left === $right) {
        return;
    }

    $tmp = $left;
    $left = $right;
    $right = $tmp;
}

$a = 1;
$b = 2;
var_dump(swap($a, $b), $a, $b);
```

以上程序输出：

```
null
int(2)
int(1)
```

试图去获取一个 void 方法的返回值会得到 **`NULL`** ，并且不会产生任何警告。这么做的原因是不想影响更高层次的方法。



### 20）`Symmetric array destructuring`

`PHP 7.1` 短数组语法（*[]*）现在作为[list()](https://www.php.net/manual/zh/function.list.php)语法的一个备选项，可以用于将数组的值赋给一些变量（包括在 `foreach` 中）。

```php
<?php
$data = [
    [1, 'Tom'],
    [2, 'Fred'],
];

// list() style
list($id1, $name1) = $data[0];

// [] style
[$id1, $name1] = $data[0];

// list() style
foreach ($data as list($id, $name)) {
    // logic here with $id and $name
}

// [] style
foreach ($data as [$id, $name]) {
    // logic here with $id and $name
}
```



### 21）类常量可见性

`PHP 7.1` 现在起支持设置类常量的可见性。

```php
<?php
class ConstDemo
{
    const PUBLIC_CONST_A = 1;
    public const PUBLIC_CONST_B = 2;
    protected const PROTECTED_CONST = 3;
    private const PRIVATE_CONST = 4;
}
```



### 22）list()现在支持键名

`PHP 7.1` 现在[list()](https://www.php.net/manual/zh/function.list.php)和它的新的*[]*语法支持在它内部去指定键名。这意味着它可以将任意类型的数组 都赋值给一些变量（与短数组语法类似）。

```php
<?php
$data = [
    ["id" => 1, "name" => 'Tom'],
    ["id" => 2, "name" => 'Fred'],
];

// list() style
list("id" => $id1, "name" => $name1) = $data[0];

// [] style
["id" => $id1, "name" => $name1] = $data[0];

// list() style
foreach ($data as list("id" => $id, "name" => $name)) {
    // logic here with $id and $name
}

// [] style
foreach ($data as ["id" => $id, "name" => $name]) {
    // logic here with $id and $name
}
```



### 23）异步信号处理

`PHP 7.1` 一个新的名为 [pcntl_async_signals()](https://www.php.net/manual/zh/function.pcntl-async-signals.php) 的方法现在被引入， 用于启用无需 ticks （这会带来很多额外的开销）的异步信号处理。

```php
<?php
pcntl_async_signals(true); // turn on async signals

pcntl_signal(SIGHUP,  function($sig) {
    echo "SIGHUP\n";
});

posix_kill(posix_getpid(), SIGHUP);
```

以上程序输出：

```
SIGHUP
```



### 24）`PHP 7.1` 新的方法

```
curl_multi_errno() - 返回上一次 curl 批处理的错误码。
curl_share_errno() - 返回共享 curl 句柄的最后一次错误号。
curl_share_strerror() - 返回错误号对应的错误消息。
session_create_id() - 创建一个新的 SESSION ID。
session_gc() - 执行 SESSION 会话垃圾回收器。
pcntl_async_signals() - 触发异步信号。
pcntl_signal_get_handler() - 给指定信息设置一个回调处理器。
```

> 更多文档：https://www.php.net/manual/zh/migration71.new-functions.php



### 25）`mcrypt` 扩展从 `PHP` 核心中移除

`PHP 7.1` : `mcrypt` 扩展已经过时了大约 10 年，并且用起来很复杂。因此它被废弃并且被 `OpenSSL` 所取代。 从 `PHP 7.2` 起它将被从核心代码中移除并且移到 `PECL` 中。



### 26）新的对象类型

`PHP 7.2` 这种新的对象类型, [object](https://www.php.net/manual/zh/language.types.object.php), 引进了可用于逆变（`contravariant`）参数输入和协变（`covariant`）返回任何对象类型。

```php
<?php

function test(object $obj) : object
{
    return new SplQueue();
}

test(new StdClass());
```



### 27）通过名称加载扩展

`PHP 7.2` 扩展文件不再需要通过文件加载 (`Unix` 下以 `.so` 为文件扩展名，在 `Windows` 下以 `.dll` 为文件扩展名) 进行指定。可以在 `php.ini` 配置文件进行启用, 也可以使用 [dl()](https://www.php.net/manual/zh/function.dl.php) 函数进行启用。



### 28）允许重写抽象方法(`Abstract method`)

当一个抽象类继承于另外一个抽象类的时候，继承后的抽象类可以重写被继承的抽象类的抽象方法。

```php
<?php

abstract class A
{
    abstract function test(string $s);
}
abstract class B extends A
{
    // overridden - still maintaining contravariance for parameters and covariance for return
    abstract function test($s) : int;
}
```



### 29）使用 `Argon2` 算法生成密码散列

`PHP 7.2` `Argon2` 已经被加入到密码散列（`password hashing`） `API` (这些函数以 `password_` 开头), 以下是暴露出来的常量:

- **`PASSWORD_ARGON2I`**
- **`PASSWORD_ARGON2_DEFAULT_MEMORY_COST`**
- **`PASSWORD_ARGON2_DEFAULT_TIME_COST`**
- **`PASSWORD_ARGON2_DEFAULT_THREADS`**



### 30）新增 `ext/PDO`（`PDO` 扩展） 字符串扩展类型

`PHP 7.2` 当你准备支持多语言字符集，`PDO` 的字符串类型已经扩展支持国际化的字符集。以下是扩展的常量：

- **`PDO::PARAM_STR_NATL`**
- **`PDO::PARAM_STR_CHAR`**
- **`PDO::ATTR_DEFAULT_STR_PARAM`**

这些常量通过**`PDO::PARAM_STR`**利用位运算 `OR` 进行计算：

```php
<?php

$db->quote('über', PDO::PARAM_STR | PDO::PARAM_STR_NATL);
```



### 31）为 `ext/PDO` 新增额外的模拟调试信息

`PHP 7.2` [PDOStatement::debugDumpParams()](https://www.php.net/manual/zh/pdostatement.debugdumpparams.php) 方法已经更新，当发送 `SQL` 到数据库的时候，在一致性、行查询（包括替换绑定占位符）将会显示调试信息。这一特性已经加入到模拟调试中（在模拟调试打开时可用）。



### 32）ext/sockets（sockets扩展）添加了地址信息

`PHP 7.2` `sockets` 扩展现在具有查找地址信息的能力，且可以连接到这个地址，或者进行绑定和解析。为此添加了以下一些函数:

- [socket_addrinfo_lookup()](https://www.php.net/manual/zh/function.socket-addrinfo-lookup.php)
- [socket_addrinfo_connect()](https://www.php.net/manual/zh/function.socket-addrinfo-connect.php)
- [socket_addrinfo_bind()](https://www.php.net/manual/zh/function.socket-addrinfo-bind.php)
- [socket_addrinfo_explain()](https://www.php.net/manual/zh/function.socket-addrinfo-explain.php)



### 33）扩展了参数类型

`PHP 7.2` 重写方法和接口实现的参数类型现在可以省略了。不过这仍然是符合 `LSP`，因为现在这种参数类型是逆变的。

```php
<?php

interface A
{
    public function Test(array $input);
}

class B implements A
{
    public function Test($input){} // type omitted for $input
}
```



### 34）允许分组命名空间的尾部逗号

`PHP 7.2` 命名空间可以在 `PHP 7` 中使用尾随逗号进行分组引入。

```php
<?php

use Foo\Bar\{
    Foo,
    Bar,
    Baz,
};
```



### 35）`PHP 7.2` 新增函数

```
stream_isatty() - 检查 stream 流是否来自 TTY。
spl_object_id() - 给当前对象分配一个整型 ID。
mb_chr() - 与 chr() 类似， mb_chr() 是针对宽字符。
mb_ord() - 与 ord() 类型，mb_ord() 是针对宽字符。
mb_scrub() - 暂时不知何用。貌似是判断字符用。
```

> 更新新增函数：https://www.php.net/manual/zh/migration72.new-functions.php



### 36）废弃的方法

```
png2wbmp()
jpeg2wbmp()
__autoload()
create_function()
```

> 更多：https://www.php.net/manual/zh/migration72.deprecated.php













