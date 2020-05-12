# 0x03.1 基础配置

在文档 https://www.php.net/manual/zh/ini.list.php 中列出了 `php.ini` 所有的配置项。配置文件中的配置项特别多，我们不可能每个配置项都非常之清晰并且掌握它。所以，我们会挑选常用且重要的配置进行讲解。



本小节，会把基础配置进行讲解。



### 1）`short_open_tag`

决定是否允许使用 `PHP` 代码开始标志的缩写形式（`<? ?>`）。如果要和 `XML` 结合使用 `PHP`，可以禁用此选项以便于嵌入使用 `<?XML ?>`。否则还可以通过 `PHP` 来输出，例如：`<?php echo '<?xml version="1.0"' ?>`。如果禁用了，必须使用 `PHP` 代码开始标志的完整形式 (`<?php ?>`)。

这一项在日常编程中主要影响我们在模板中的使用方式。比如，我们经常会在 `HTML` 中嵌入 `PHP` 代码。如果没有开启短标记。

则：

```
......
<span>姓名：<?php echo $username; ?></span>
......
```

如果开启短标记之后：

```
......
<span>姓名：<?=$username; ?></span>
......
```

> 本指令也会影响到缩写形式 **`**，它和 **`** 等价。使用此缩写需要 `short_open_tag` 的值为 `On`。 从 `PHP 5.4.0` 起， `<?=`总是可用的。

```ini
short_open_tag = On
```



### 2）`asp_tags`

除了通常的 `<?php ?>` 标志之外还允许使用 `ASP` 风格的标志 `<% %>`。这也包括了输出变量值的缩写 `<%= $value %>`。

> 该配置已经从 `PHP 7.0.0` 版本移除。后续的版本将不再可使用。

说起这个配置的存在，是因为当初 `PHP` 发展的初期，为了让开发 `ASP` 的开发者能轻松融入 `PHP` 的大家庭。于是，特别给他们定制了一个配置。方便他们能快速融入进来。`PHP` 发展至今，已经形成了一定的开发者数量，无须再迎合这种历史情况了。并且， `ASP` 语言已经逐渐没落，开发者数量也少之又少。于是 `PHP 7.0.0` 就彻底移除了。而在之前的几个版本也默认关闭了这一项的配置。

```ini
asp_tags = Off
```



### 3）`precision`

浮点数中显示有效数字的位数。默认值 14。设置为 -1 时 `PHP` 会以使用一种改进的四舍五入的算法。通常保存默认即可。除非您有特殊的要求。迫使浮点数返回指定的小数位。

```ini
precision = 14
```



### 4）`serialize_precision`

序列化时浮点数中显示有效数字的位数。默认值 -1。设置为 -1 时 `PHP` 会以使用一种改进的四舍五入的算法。通常保存默认即可。除非您有特殊的要求。迫使浮点数返回指定的小数位。

```ini
serialize_precision = -1
```



### 5）`disable_functions`

本指令允许你基于]安全原因禁止某些函数。接受逗号分隔的函数名列表作为参数。 `disable_functions` 不受该配置的影响。

比如，一些函数可能会导致黑客利用导致系统承受不必要的风险威胁。此时，我们可以通过该函数禁用这些函数在系统中使用。

如：

```ini
disable_functions=exec,system,proc_open
```



### 6）`disable_classes`

本指令可以使你出于安全的理由禁用某些类。用逗号分隔类名。`disable_classes` 不受安全模式的影响。 本指令只能设置在 `php.ini` 中。例如不能将其设置在 `httpd.conf`。



### 7）`expose_php`

决定是否暴露 `PHP` 被安装在服务器上（例如在 Web 服务器的信息头中加上其签名：`X-Powered-By: PHP/5.3.7`)。

推荐将此配置设置为 `Off`。避免由于版本泄漏导致别有用户的黑客针对性的攻击。

```ini
expose_php = Off
```



### 8）`memory_limit`

设置 `PHP` 脚本运行时允许使用的最大内存。默认值：`128M`。

通常情况下 `128M` 限制足够了。假如，项目中要处理一些占用内存偏高的工作，那么内存可能就不够用。比如，`Excel` 导入导出。导入几万可能没啥大问题，导入几十万可能就力不从心了。具体设置多少合适，具体要看应用中是否存在这种高内存消耗的需求。

```ini
memory_limit = 128M
```



### 9）`realpath_cache_size`

设置真实路径缓冲区大小。默认值：`2M`。

该配置项属于性能优化项。它与 `realpath_cache_ttl` 两个配置直接影响了 `realpath_cache` 缓存机制。



`require`、`require_once`、`include`、`include_once` 这四个语句我们日常开发中经常会用到。在命名空间特性出来之后，很多框架已经不再需要使用这四个语句来加载文件了。但是，这四个语句依然非常有用。



如果用这类语句去包含文件(相对路径)的时候，那么 `PHP` 会去 `php.ini` 的 `include_path` 配置指向的路径中去查找相关文件。如果一个应用中存在大量的这种加载语句的调用，如果每次都去调用 `include_path` 查找文件，势必会造成对应的性能影响。



为了避免这种性能影响，很多的 `PHP` 性能优化的文章就提倡我们在加载文件的时候使用绝对路径。以此减少路径查询导致的性能问题。



从 `PHP 5.1.0` 开始引入了 `RealPathCache` 缓存机制。可以把 `PHP` 所用到的文件的路径进行缓存，以便 `PHP` 再使用这些文件的时候不需要再去 `include_path` 中查找，以此加快 `PHP` 的执行速度。

```ini
realpath_cache_size = 2M
```



### 10）`realpath_cache_ttl`

设置 `realpath_cahce` 的缓存存活时间。默认值：`120`。单位：秒。

对于不经常修改的生产环境来说，这个时间可以设置得更大一些。

由于 `realpath` 会展开 `symlink`(即软链接) ，所以如果你使用修改 `symlink` 目标这种方式发布应用的新版本，`realpath cache` 会导致一些问题的出现：当您修改 `symlink` 使用其指向一个新的 `release` 目录的时候，由于 `realpath cache` 所缓存的内存还没有过期，于是就会出现应用使用的还是旧的 `release` ，直到 `realpath cache` 所缓存内容过期失效为止。或者重启 `PHP-FPM`。

```ini
realpath_cache_ttl = 120
```



### 11）`request_order`

该配置决定了 `$_REQUEST` 超全局变量数据被 `$_GET`、`$_POST`、`$_COOKIE` 填充的顺序。俗称 `GPC`。后面的数值会覆盖前面的数据。

如果您的应用系统之中大量使用 `$_REQUEST` 来做一些关键应用。一定要注意它们的覆盖顺序。

默认的数据覆盖顺序：`CGP`。即 `$_POST` 的数据优先级最高，`$_COOKIE` 的优先级最低。

```ini
request_order = "CGP"
```



### 12）`include_path`

设置 `PHP` 在包含相对路径的文件时，去哪些目录找这文件的目录路径。上面已经涉及到 `PHP` 程序中加载文件的机制。这里不再赘述。



### 13）`extension_dir`

这个参数设置第三方扩展存放的目录。这个配置可以说每个`PHP` 开发都必须了解且会配置。在第 1 章中的第 6 小节，我们已经进行了详细讲解。

```ini
extension_dir = "/usr/local/php72/lib/php/extensions/no-debug-non-zts-20170718/"
```



### 14）`extension`

设置 `PHP` 启动时可以动态加载的扩展。

如果设置的时相对路径，则会去 `extension_dir` 指向的目录查找。

```ini
extension=redis.so
```

或

```ini
extension=/data/php72/redis.so
```



### 15）`zend_extension`

与 `extension` 配置类似。只不过这个指令只能加载那些在 `Zend` 引擎层使用的扩展。

比如，`xdebug`。

```ini
zend_extension=xdebug.so
```

不同配置加载的扩展的顺序以及时机不一样。



### 16）`open_basedir`

这是一个安全配置。限制 `PHP` 只能在该配置指定的目录下进行操作。这就避免了恶意攻击系统。比如，恶意黑客通过漏洞删除系统核心文件，导致服务器宕机。

通常该配置设置为当前项目的根目录。根自己的需求来设定。



### 17）`max_execution_time`

设置 `PHP` 脚本最大执行时间。在 `CLI` 模式下无效。因为 `CLI` 模式无最长时间限制。

```ini
max_execution_time = 60
```





