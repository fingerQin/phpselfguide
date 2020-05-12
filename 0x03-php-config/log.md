# 0x03.4 错误与日志配置

`PHP` 日志配置，离不开与 `PHP` 错误的配置。



### 1）`display_errors`

该选项设置是否将错误信息作为输出的一部分显示到屏幕，或者对用户隐藏而不显示。

它只是关闭用户对用户的错误显示。它并不影响我们通过日志接受错误。

我们可以在 `PHP` 配置文件以及 `PHP` 代码中设置。

**`PHP` 配置文件：**

```ini
display_errors = 1
```

**`PHP` 代码设置:**

```php
<?php
ini_set('display_errors', 1);
```



### 2）`display_startup_errors`

即使 `display_errors` 设置为开启, `PHP` 启动过程中的错误信息也不会被显示。强烈建议除了调试目的以外，将 `display_startup_errors` 设置为关闭。

`PHP` 启动过程中会解析配置并且加载第三方扩展等操作。如果有一些必须设置的配置没有设置，加载的扩展不存在等，就会在启动的时候报错。所以，基本上这类错误是我们不应该犯的。所以，我们要关闭它避免显示给用户。

```ini
display_startup_errors = Off
```



### 3）`log_error`

设置是否将脚本运行的错误信息记录到服务器错误日志或者[error_log](https://www.php.net/manual/zh/errorfunc.configuration.php#ini.error-log)之中。注意，这是与服务器相关的特定配置项。设置为 `On` 的时候错误日志会写入 `error_log` 指向的日志文件中。

> 无论 `display_error` 开启与否，都不影响错误日志的写入。

强烈推荐设置为：`On`。

```ini
log_errors = On
```



### 4）`log_errors_max_len`

设置 `log_errors` 的最大字节数. 在 [error_log](https://www.php.net/manual/zh/errorfunc.configuration.php#ini.error-log) 会添加有关错误源的信息。默认值为：1024，如果设置为 0 表示不限长度。该长度设置对记录的错误，显示的错误，以及 [$php_errormsg](https://www.php.net/manual/zh/reserved.variables.phperrormsg.php) 都会有限制作用。

也就是说，当错误发生时的错误信息写入日志文件中时，超过这个长度将会被忽略。通常来说 1024 个字节完全够用了。

如果在检查日志的时候发现错误日志信息超出这个日志长度的时候，可以通过将此值加大。

```ini
log_errors_max_len = 1024
```



### 5）`error_logs`

设置脚本错误将被记录到的文件。该文件必须是 `web` 服务器用户可写的。如果特殊值 `syslog` 被设置，则将错误信息发送到系统日志记录器。在 `Unix` 以及类似系统上，使用的是 `syslog(3)` ，而在 `Windows NT` 类系统上则为事件日志。`Windows 95` 上不支持系统日志记录。参见： [syslog()](https://www.php.net/manual/zh/function.syslog.php) 。如果该配置没有设置，则错误信息会被发送到 `SAPI` 错误记录器。例如，出现在 `Apache` 的错误日志中，或者在 `CLI` 中发送到 `stderr`。

```ini
error_logs = syslog
error_logs = /data/php/logs/php-error.log
```



### 6）`error_reporting`

设置错误报告的级别。该参数可以是一个任意的表示二进制位字段的整数，或者常数名称。错误级别和常数是在 [预定义常量](https://www.php.net/manual/zh/errorfunc.constants.php)定义的，在 `php.ini` 之中也有专门的说明。在程序运行时，还可以通过 [error_reporting()](https://www.php.net/manual/zh/function.error-reporting.php) 函数进行设置。请查看 [display_errors](https://www.php.net/manual/zh/errorfunc.configuration.php#ini.display-errors) 了解详情。

这个配置只影响 `display_errors` 为 `On` 时向用户回显的错误信息内容。并不会影响日志的记录。

在开发环境，最好将该项设置为最低级别。保证 `notice` 级别的错误也能正常回显。这样有利于我们写出更加健壮的代码。

>在 `PHP` 5 之中，提供了一个新的错误级别 **`E_STRICT`**。 因为 **`E_STRICT`** 并不包含在 **`E_ALL`** 之中，你必须明确启用才能显示这个类别的错误信息。在开发阶段启用 **`E_STRICT`** 会有一些好处。严格的信息将帮助你使用最新和最好的建议的方法来编写代码，例如它会警告你使用了将被废弃的函数。

**开发环境推荐设置：**

```ini
error_reporting = E_ALL & E_STRICT
```



### 7）`ignore_repeated_errors`

不记录重复的信息。重复的错误必须出现在同一个文件中的同一行代码上，除非 `ignore_repeated_source` 设置为 `true`。

通常保持默认的配置即可。



### 8）`ignore_repeated_source`

忽略重复消息时，也忽略消息的来源。当该设置开启时，重复信息将不会记录它是由不同的文件还是不同的源代码行产生的。



> 还有其他日志配置参数。大多数都不重要。保持默认值即可。以上都是常用重要的配置。更多错误与日志配置:https://www.php.net/manual/zh/errorfunc.configuration.php

