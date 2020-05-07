# 0x02.2 PHP 5.4 版本特性

`PHP 5.4` 版本也可以说是一个非常重大的版本。它增加了很多的特性。只有了解这些重要的特性，才能避免使用已弃用的特性与方法，或使用即将弃用的方法导致未来版本升级带来的风险。其次，新增的方法或参数可能会让自己的代码少写一些代码提升性能与维护。

> 关于 `PHP 5.4` 完整的弃用、移除、新增等特性。官方文档：https://www.php.net/manual/zh/migration54.php

下面我们会挑核心重点进行说明。面试的时候也算是重点考察的范围。



### 1）新增的函数

```
hex2bin() - 转换十六进制字符串为二进制字符串。
http_response_code() - 获取/设置响应的 HTTP 状态码。
get_declared_traits() - 返回所有已定义的 traits 的数组。
getimagesizefromstring() - 从字符串中获取图像尺寸信息。
stream_set_chunk_size() - 设置资源流区块大小。
socket_import_stream() - 导入一个资源流。
trait_exists() - 检查指定的 trait 是否存在。
header_register_callback() - 调用一个 header 函数。
session_status() - 返回当前会话状态。
session_register_shutdown() - 关闭会话时注册回调函数。
```

> 上面只列举了常用的新增函数。更多：https://www.php.net/manual/zh/migration54.functions.php



### 2）新增支持 traits

`Traits` 是一种代码利用机制。减少 `PHP` 单继承带来的限制。



### 3）新增短数组语法

比如 

`$a = [1, 2, 3, 4];`  

或 

`$a = ['one' => 1, 'two' => 2, 'three' => 3, 'four' => 4];`



### 4）支持函数返回数组的成员访问解析

```php
<?php
function test() {
	return ['username' => 'fingerQin'];
}

echo test()['username'];
```



### 5）闭包支持 `$this`

```php
<?php

class Test
{
    public function testing()
    {
        return function() {
            var_dump($this);
        };
    }
}

$object = new Test;
$function = $object->testing();
$function();
```

输出：

```
object(Test)#1 (0) {
}
```



### 6）`<?=` 写法总是可用

`<?=` 与 `<? echo` 等价。不受 `ini` 配置文件 `short_open_tag` 参数影响。



### 7）新增在实例化时访问类成员

在之前的版本要访问一个对象里面的方法或属性，我们只能先 `new` 一个对象出来。再去访问。现在有了更简洁的方式：

```
(new Obj())->func();
```



### 8）现在支持 `Class::{expr}()` 语法

笔者很少用这个特性。大家想深入了解的可以研究研究。



### 9）新增二进制直接量

例如：`0b001001101`。



### 10）改进解析错误信息和不兼容参数的警告



### 11）SESSION 扩展现在能追踪文件的上传进度 

上传进度这个可谓非常的实用。

请阅读文档了解更多：https://www.php.net/manual/zh/session.upload-progress.php



### 12）内置用于开发的 `CLI` 模式的 `web server`

`PHP 5.4.0` 最大的特点之一，就是内置了一个 `Web Server`。

请阅读文档：https://www.php.net/manual/zh/features.commandline.webserver.php



### 13）新增两个与 `Session` 相关的类

```
SessionHandler
SessionHandlerInterface - 继承这个接口，重写里面的方法可以实现将 SESSION 写入任何 DB。如：Redis、SSDB、MySQL 等。
```



### 14）不再支持安全模式。



### 15）移除魔术引号

魔术引号的出现是解决安全问题。但是同时也带来了编程的困扰。在 `PHP 5.4` 正式退出历史舞台。



### 16）新增保留关键字

```
trait
callable
insteadof
```



### 17）移除方法

```
define_syslog_variables()
import_request_variables()
session_is_registered()
session_register()
session_unregister() 
mysqli_bind_param()
mysqli_bind_result()
mysqli_client_encoding()
mysqli_fetch()
mysqli_param_count()
mysqli_get_metadata()
mysqli_send_long_data()
mysqli::client_encoding()
mysqli_stmt::stmt()
```



### 18）移除了 `sqlite` 扩展

之前的版本 `sqlite` 扩展是内置扩展，随 `PHP` 一起发布。`PHP 5.4` 之后，如果要使用 `sqlite` 必须通过自己安装。



### 19）`OpenSSL` 支持 `AES` 加密算法



### 20）增加新的哈希算法： `fnv132`、 `fnv164` 及 `joaat`

