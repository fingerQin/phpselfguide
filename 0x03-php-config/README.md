# 0x03 PHP 配置

`PHP` 配置对我们深入了解 `PHP` 以及用好 `PHP` 是非常之重要的。

`php.ini` 所有配置项的说明文档：https://www.php.net/manual/zh/ini.list.php

`Apache` 与 `php.ini` 设置文档：https://www.php.net/manual/zh/configuration.changes.php

`.user.ini` 配置文件：https://www.php.net/manual/zh/configuration.file.per-user.php

接下来我们会挑选常用且重点的配置进行说明讲解。



### 1）可修改范围

这些模式决定着一个 `PHP` 的指令在何时何地，是否能够被设定。手册中的每个指令都有其所属的模式。例如有些指令可以在 `PHP` 脚本中用 [ini_set()](https://www.php.net/manual/zh/function.ini-set.php) 来设定，而有些则只能在 `php.ini` 或 `httpd.conf` 中。

例如 [output_buffering](https://www.php.net/manual/zh/outcontrol.configuration.php#ini.output-buffering) 指令是属于 `PHP_INI_PERDIR`，因而就不能用 [ini_set()](https://www.php.net/manual/zh/function.ini-set.php) 来设定。但是 [display_errors](https://www.php.net/manual/zh/errorfunc.configuration.php#ini.display-errors) 指令是属于 `PHP_INI_ALL` 因而就可以在任何地方被设定，包括 [ini_set()](https://www.php.net/manual/zh/function.ini-set.php)。



| 模式             | 含义                                                         |
| :--------------- | :----------------------------------------------------------- |
| `PHP_INI_USER`   | 可在用户脚本（例如 [ini_set()](https://www.php.net/manual/zh/function.ini-set.php)）或 [Windows 注册表](https://www.php.net/manual/zh/configuration.changes.php#configuration.changes.windows)（自 `PHP 5.3` 起）以及 `.user.ini` 中设定 |
| `PHP_INI_PERDIR` | 可在 `php.ini`，`.htaccess` 或 `httpd.conf` 中设定           |
| `PHP_INI_SYSTEM` | 可在 `php.ini` 或 `httpd.conf` 中设定                        |
| `PHP_INI_ALL`    | 可在任何地方设定                                             |

> 注：当我们要修改 `php.ini` 里面配置的时候，首先除了要确定其配置的作用外，还要确定它的可修改范围。否则会出现改了之后不会生效的情况。



### 2）`ini_set()` 修改配置

`ini_set()` 只能修改配置可修改范围 `PHP_INI_USER` 的配置项。那么，我们要怎样确定一个配置的可修改范围呢？

可以在官方提供的文档中查询：https://www.php.net/manual/zh/ini.list.php

> 注：通过 `ini_set()` 设置的值会覆盖 `httpd.conf`、`.htaccess`与 `php.ini` 中配置的值。

以设置 `PHP` 脚本最大执行时间为例：

```php
<?php
ini_set('max_execution_time', 90); // 设置最大执行时间 90 秒。
```

与 `ini_set()` 方法对应的方法是 `ini_get()`。

`ini_get()` 是读取配置的值。

```php
<?php
echo ini_get('max_execution_time');
```



### 3）`.htaccess`

以 `Apache` 作为 `PHP` 的前置 `Web` 服务器的时候，这个文件通常位于根目录下。在此文件设置的值会覆盖 `httpd.conf` 以及 `php.ini` 中设置的值。

要让 `.htaccess` 文件生效，必须在 `Apache` 的 `httpd.conf` 配置文件中开启重写。

```ini
LoadModule rewrite_module modules/mod_rewrite.so
```

找到上面这一行将前面的注释 `#` 去掉。

然后再找到 `AllowOverride` 设置为 `all`。

```ini
AllowOverride all
```

> `PHP_INI_PERDIR` 以及 `PHP_INI_ALL` 两种可修改范围的配置，可以在 `.htaccess` 文件中设置。

示例：

```ini
php_value max_execution_time 200
php_value memory_limit 1024M
php_flag display_errors on
```



#### 3.1）`php_value` 指令

设定指定的值。只能用于 `PHP_INI_ALL` 或 `PHP_INI_PERDIR` 类型的指令。要清除先前设定的值，把 `value` 设为 `none`。

该指令用于在 `.htaccess` 与 `httpd.conf` 中。通常用于修改不属于布尔类型的配置指令。

> 不要用 `php_value` 设定布尔值。



#### 3.2）`php_flag` 指令

`php_flag name on|off`
用来设定布尔值的配置指令。仅能用于 `PHP_INI_ALL` 和 ` PHP_INI_PERDIR` 类型的指令。



### 4）`httpd.conf` 更改 `PHP` 配置

`httpd.conf` 是 `Apache` 的配置文件。在此文件中设置的值会覆盖在 `php.ini` 中配置的值。

可修改范围 `PHP_INI_ALL`、`PHP_INI_SYSTEM`、`PHP_INI_PERDIR` 三种可以在 `httpd.conf` 配置文件中修改。

示例：

```php
<IfModule mod_php5.c>
    php_admin_flag display_errors on
    php_flag short_open_tag on
    php_admin_value session.gc_maxlifetime 3600
    php_value date.timezone PRC
</IfModule>
```

上面的示例是 `PHP5`  的配置示例。

它出现了两种新的指令：`php_admin_flag` 与 `php_admin_value`。

在 `.htaccess` 中我们使用了 `php_flag` 与 `php_flag` 两种指令。现在我们来认识一下新的指令。



#### 4.1）`php_admin_flag` 指令

设定指定的指令的值。与 `php_flag` 指令相同。但不能用于 `.htaccess` 文件。任何用 `php_admin_value` 设定的指令都不能被 `.htaccess` 或 `virtualhost` 中的指令覆盖。想要清除先前设定的值，把 `value` 设为 `none`。

> 注：1）只能在 `httpd.conf` 使用。2）不能被 `.htaccess` 或 `virtualhost` 覆盖。



#### 4.2）`php_admin_value` 指令

用来设定布尔值的配置指令。与 `php_admin_value` 指令相同。但不能用于 `.htaccess` 文件。任何用 `php_admin_flag` 设定的指令都不能被 `.htaccess` 或 `virtualhost` 中的指令。



> 注：`PHP` 常量不存在于 `PHP` 之外。例如在 `httpd.conf` 中不能使用 `PHP` 常量如 `E_ALL` 或 `E_NOTICE` 来设定 `error_reporting` 指令。因为其无意义，实际等于 0。应该用相应的掩码值来替代。这些常量可以在 `php.ini` 中使用。





