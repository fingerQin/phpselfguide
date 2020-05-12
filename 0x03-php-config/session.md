# 0x03.2 SESSION 配置

在 `Web` 系统开发中，`SESSION` 会话可谓是一个非常重要且常用的技术点了。

关于 `PHP SESSION` 所有的配置全部在这里：https://www.php.net/manual/zh/session.configuration.php

示例：

```ini
session.auto_satrt = 0
session.name = "PHPSESSID"
session.save_path = "/tmp"
session.save_handler = "files"
session.gc_probability = 1
session.gc_divisor = 100
session.gc_maxlifetime =  1440
session.serialize_handler = php
session.cookie_lifetime = 0
session.cookie_path = "/"
session.cookie_domain = ""
session.cookie_secure = ""
session.cookie_httponly = ""
session.cookie_samesite = ""
session.use_strict_mode = 0
session.use_cookies = 1
session.use_only_cookies = 1
session.referer_check = ""
session.cache_limiter = "nocache"
session.cache_expire = 180
session.use_trans_sid = 0
session.trans_sid_tags = "a=href,area=href,frame=src,form="
session.trans_sid_hosts = ""
session.sid_length = 32
session.sid_bits_per_character = 4
session.upload_progress.enabled = 1
session.upload_progress.cleanup = 1
session.upload_progress.prefix = "upload_progress_"
session.upload_progress.name = "PHP_SESSION_UPLOAD_PROGRESS"
session.upload_progress.freq = "1%"
session.upload_progress.min_freq = 1
session.lazy_write = 1
```

`SESSION` 配置项看似很多，实则常用的就那么几个。接下来我们一项一项介绍这些配置。



### 1）`session.auto_satrt`

是否自动启动 `SESSION`。推荐设置为 0。需要的时候才开启。比如，假如我们只是用 `PHP` 开发 `API` 接口。那么，`SESSION` 开启并没有意义。反而会导致服务器性能下降。



### 2）`session.name`

指定会话名以用做 `cookie` 的名字。只能由字母数字组成，默认为 `PHPSESSID`。

因为 `SESSION` 会话生成的 `ID` 以此配置指定的名字在浏览器 `Cookie` 中保存。我们可以混淆黑客的视听，更改为 `JAVASSID`，让黑客误以咱们服务器是 `Java` 系统。



### 3）`session.save_path`

`SESSION` 存放的目录。默认值为：`/tmp`。当 `ssession.save_handler` 驱动为 `files` 该配置才生效。通常不建议保存在 `/tmp` 中。因为，当我们服务器存在多个 `web` 应用的时候，会将所有的应用的 `SESSION` 存放在该目录，会出现两种不利的情况：1）磁盘 IO 性能不高。2）有概率发生碰撞。



### 4）`session.save_handler`

定义 `SESSION` 会话数据存放与获取以何种驱动方式处理。默认：`files`。通过此配置，我们可以将 `SESSION` 存放到 `Redis`、`Memcached`、`Mysql` 等服务器。

在实际开发中，我们通常是不会直接在 `php.ini` 配置文件中更改此配置达到存放不到服务器的目的。因为，不同的应用系统保存的驱动不一样。比如，有的项目保存在文件，有的保存在 `Redis`。

实际开发中我们是以实现 `SessionHandlerInteface` 接口实现存放到不同服务器。



### 5）`session.gc_maxlifetime`

`SESSION` 有效期。每一次访问有效期会被重置为此配置设定的时间。如果超出此值设定的时间 `SESSION` 会被清除。



### 6）`session.serialize_handler`

`session.serialize_handler` 定义用来序列化／解序列化的处理器名字。 当前支持 `PHP` 序列化格式 (名为 `php_serialize`)、 `PHP` 内部格式 (名为 `php` 及 `php_binary`) 和 `WDDX` (名为 `wddx`)。 如果 `PHP` 编译时加入了 [WDDX 支持](https://www.php.net/manual/zh/ref.wddx.php)，则只能用 `WDDX`。 自 `PHP 5.5.4` 起可以使用 `php_serialize`。 `php_serialize` 在内部简单地直接使用 `serialize/unserialize` 函数，并且不会有 `php` 和 `php_binary` 所具有的限制。 使用较旧的序列化处理器导致 `$_SESSION` 的索引既不能是数字也不能包含特殊字符(`| and !`) 。 使用 `php_serialize` 避免脚本退出时，数字及特殊字符索引导致出错。 默认使用 `php`。

`session.serialize_handler` 存在以下几种

- `php_binary` 键名的长度对应的 `ascii` 字符 + 键名 + 经过 `serialize()` 函数序列化后的值
- `php` 键名+竖线`(|)` + 经过 `serialize()` 函数处理过的值
- `php_serialize` 经过 `serialize()` 函数处理过的值，会将键名和值当作一个数组序列化。

> 通常保持默认值：`php`。



### 7）`session.cookie_lifetime`

设置 `SESSION` 对应保存在浏览器 `Cookie` 中的 `SESSION ID` 的有效期。默认为：0。

0 代表浏览器关闭 `SESSION` 失效。

对 `HTTP` 协议有一定了解的开发者都知道它是一个无状态的协议。每一次请求互相之间是独立的。您没办法上一次请求与下一次请求是否为同一个用户发起。于是就有人提出了 `SESSION` 与 `COOKIE` 实现了有状态机制。而 `SESSION` 是保存在服务器端的用户会话数据。但是要识别这些数据属于哪个用户，是由浏览器 `Cookie` 中保存的 `SESSION ID` 来实现的。

大家可以在开启 `PHP SESSION` 的时候，会在浏览器的 `Cookie` 里面发现一个以 `PHPSESSID` 命名的 `Cookie`。

> 面试的时候经常会有面试官会问：怎样设置一个固定时间到期 `SEESION` 会话失效？
>
> 假如：30 分钟后 `SESSION` 会话自动失效。
>
> 1）设置 `session.gc_maxlifetime` `SESSION`  有效期为：1800。
>
> 2）设置 `session.cookie_lifetime` 有效期为：1800。
>
> 为什么要设置 `session.gc_maxlifetime` 的值为 1800 ？
>
> 如果不设置为 1800，当 `COOKIE` 没失效，`SESSION` 服务器端的用户会话数据就因为 `GC` 机制而被销毁了。



### 8）`session.use_cookies`

设置是否使用 `Cookie` 保存会话 `ID`。默认值为：1。

当为 0 的时候，`SSESION ID` 将不会保存在 `Cookie` 中，而会以将 `SESSION ID` 附加到 `URL` 地址后面。

什么时候会用到这个配置呢？

笔者在 `2010` 年的时候为初代智能手机开发手机触屏网站的时候，部分浏览器不兼容 `Cookie` 机制。于是，我们采用通过页面 `URL` 传递 `SESSION ID` 来解决会话问题。



### 9）`session.cookie_domain`

设置 `Cookie` 会话的生效域名。默认为无。代表设置当前访问的域名。

```php
<?php
$lifetime = time() + 3600; // 失效时间。
$domain = "test.com";
setcookie("token", "123", $lifetime, "/", $domain);
```

通常我们无须设置这一项。只需要在调用 `setcookie()` 方法的时候再使用。它主要解决了单点登录会话的问题。

> 单点登录 `SSO`（`SingleSignOn`）场景：
>
> 假如我们有一个电商系统，社区系统。想把两个系统的用户数据统一管理。此时，我们会开发一个单点登录系统。实现统一的注册、登录、密码修改、密码找回等功能。此时希望登录之后，电商系统与社区系统都能无正常登录。那么，此时登录成功之后的 `Cookie` 要分别为两个系统域名生成。



### 10）`session.cookie_path`

设置 `Cookie` 生效的路径。默认值：`/`。

比如，我们只想 `Cookie` 针对域名下的某个子路径生效。就能用得到此值。但是，通常我们不会修改此配置实现。而是在调用 `setcookit()` 方法时设置此参数。

设置 Cookie 针对 `test.com/shop/` 生效。

```php
<?php
$lifetime = time() + 3600; // 失效时间。
$domain = "test.com";
setcookie("token", "123", $lifetime, "/shop/", $domain);
```

设置 `/shop/` 生效的 `Cookie`，在其他路径是获取不到此 `Cookie` 的。父路径可以在子路径获取，而子路径在同级或父级获取不到。



> 以上 `SESSION` 配置属于常用且重要的配置。其他参数只作了解，保持默认值即可。

