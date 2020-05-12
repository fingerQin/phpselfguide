# 0x03.3 Cookie 配置

在 3.2 小节中，我们已经讲了几个 `Cookie` 与 `Session` 的配置。那已经基本上代表了所有的 `Cookie` 配置。所以，本小节我们只讲几个 `Cookie` 本身的一些特性以及在浏览器中的表现。



### 3.1）设置 `Cookie`

在 `PHP` 中我们通过 `setcookie()` 方法来设置 `Cookie`。

关于 `setcookit()` 方法的文档：https://www.php.net/manual/zh/function.setcookie.php

```
setcookie ( string $name [, string $value = "" [, int $expire = 0 [, string $path = "" [, string $domain = "" [, bool $secure = false [, bool $httponly = false ]]]]]] ) : bool
```

- 第一个参数是 `Cookie` 的名称。
- 第二个参数是 `Cookie` 的值。
- 第三个参数是 `Cookie` 的有效期。这是个 Unix 时间戳，即 Unix 纪元以来（格林威治时间 1970 年 1 月 1 日 00:00:00）的秒数。
- 第三个参数是 `Cookie` 生效的域名子路径。
- 第四个参数是 `Cookie` 作用于域名。
- 第五个参数是 `Cookie`  是否仅仅通过安全的 `HTTPS` 连接传给客户端。
- 第六个参数是 `Cookie` 仅可通过 `HTTP` 协议访问。



使用该方法要注意的一些事项：

- 默认情况下，`Cookie` 对 `HTTP` 和 `HTTPS` 都生效。如果只想对 `HTTPS` 生效，请将第五个参数设置为 `TRUE`。
- 调用 `setcookit()` 方法之前，不能产生任何输出。比如：`echo`、`var_dump()`、`print_r()` 等带输出功能的方法输出的内容。
- 在发送 `Cookie` 到客户端的时候，值的部分被自动 `urlencode` 编码， 收到 `Cookie` 的时候，会自动解码，并赋值到 `$_COOKIE` 上。如果不想被编码，可以使用 `setrawcookit()` 方法代替。
- 有效期设置为 0 代表浏览器关闭就失效，浏览器会自动清除。但是，由于现在浏览器是多 Tab 形式运行。这里的关闭必须是把整个浏览器全部关闭才生效。
- 浏览器有 `Cookie` 数量限制。每个浏览器 `Cookie` 的限制数量各不相同。通常 20 个 `Cookie` 是最合适的。请勿过度使用 `Cookie` 保存太多的数据。否则，兼容性会成为最大的问题。当 `Cookie` 失效的时候请检查是否超过了浏览器限制的数量。
- 浏览器有 `Cookie` 值大小限制。这个值大小指的是所有 Cookie 加起来的值大小。4095 个字节是最安全的。浏览器版本越来越新之后，这个限制可能会发生变化。但是为了保险起见，最好设置为 4095 个字节内。



> 关于浏览器对 Cookie 的限制，经常会有面试官问题。所以，大家一定要掌握好。



### 3.2）`Cookie` 保存数据

通过带 `array` 标记的 `Cookie` 名称，也可以把 `Cookie` 设置成数组。 如果有数组元素，可以把它放进 `Cookie` 里； 脚本接收到时，`Cookie` 名称里的值会是一个数组：

```php
<?php
// 设置 Cookie
setcookie("cookie[three]", "cookiethree");
setcookie("cookie[two]", "cookietwo");
setcookie("cookie[one]", "cookieone");

// 网页刷新后，打印出以下内容
if (isset($_COOKIE['cookie'])) {
    foreach ($_COOKIE['cookie'] as $name => $value) {
        $name = htmlspecialchars($name);
        $value = htmlspecialchars($value);
        echo "$name : $value <br />\n";
    }
}
```

以上程序输出：

```
three : cookiethree
two : cookietwo
one : cookieone
```



