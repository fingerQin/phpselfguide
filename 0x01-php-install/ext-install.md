# 0x01.6 PHP 扩展安装

`PHP` 扩展安装是一名合格 `PHPer` 必须具备的技能之一。而 `PHP` 扩展安装又要考虑版本差异性。以及配置文件的修改。



### 1）准备工作

#### 1.1）设置扩展目录

`PHP` 扩展编译完成之后，会将 `so` 文件存放到 `PHP` 安装目录下的 `lib/php/extensions/no-debug-non-zts-20170718` 下。

后面的 `no-debug-non-zts-20170718` 与 `PHP` 版本的三个东西存在关联：

- `PHP` 是否为线程安全版本。如果不是线程安全版本就会带 `non-zts` 字样。
- `PHP` 是否开启 `debug` 模式。如果是关闭状态则会带 `no-debug` 状态。
- `Zend` 引擎内核时间。通过执行 `phpize` 会得到相关的版本信息。

```
[root@localhost redis-5.2.1]# /data/server/php/php72/bin/phpize 
Configuring for:
PHP Api Version:         20170718
Zend Module Api No:      20170718
Zend Extension Api No:   320170718
```

`PHP Api Version 与 Zend Module Api No` 两个信息不总是相同。而上述的目录中带的日期信息是 `Zend Module Api No` 指向的日期。



那么，要怎么得到这个值呢？

最方便的办法是就编译扩展之后，会显示扩展存放的路径。而这个路径就是所有扩展以后的存放路径。

如：

```
[root@localhost redis-5.2.1]# make install
Installing shared extensions:     /data/server/php/php72/lib/php/extensions/no-debug-non-zts-20170718/
```



扩展编译成功之后，并不会自动生效。需要告诉我们扩展去哪加载。

所以，要对 `php.ini` 配置文件进行修改：

```ini
extension_dir="/data/server/php/php72/lib/php/extensions/no-debug-non-zts-20170718/"
```



#### 1.2）去哪下载扩展

`PHP` 已经为我们专门准备了一个官方下载扩展的网站：http://pecl.php.net 。

当然，有很多人的扩展并不会提交到官网。或者最新版本提交到官网的速度比较慢。他们通常会选择在 `GitHub` 创建一个仓库。大家可以直接从上面下载回来编译安装。当然，通常不建议这么做。



#### 1.3）`PHP` 不同版本的扩展选择

目前广泛使用的 `PHP` 版本主要有两个：`PHP 5` 与 `PHP 7`。而 `PHP 5` 与 `PHP 7` 的扩展并不能互相兼容。主要是 `PHP 7` 底层的 `Zend` 引擎做了极大的升级。导致扩展也无法互相兼容使用。那么，我们去选择扩展的时候要注意查看扩展的使用说明：**是否支持您的 `PHP` 版本**。

即使是专属为 `PHP 5` 或 `PHP 7` 编写的扩展，也要睁大眼睛查看是否支持小版本。比如，很多扩展更新速度未跟上 `PHP` 小版本的迭代，或已经停止更新。那么，一定不要安装。有可能安装不上，也有可能安装成功之后无法得到预期的结果。甚至会带来安全隐患。



### 2）安装扩展

#### 2.1）常用扩展安装

我们以安装常用的 `redis` 扩展为例。

**`PHP 5.x` 版本扩展**

```
$ wget http://pecl.php.net/get/redis-4.3.0.tgz
$ tar zxvf redis-4.3.0.tgz
$ cd redis-4.3.0
$ /data/server/php/php56/bin/phpize
$ ./configure --with-php-config=/data/server/php/php56/bin/php-config
$ make && make install
```

**`PHP 7.x` 版本扩展**

```
$ wget http://pecl.php.net/get/redis-5.2.2.tgz
$ tar zxvf redis-5.2.2.tgz
$ cd redis-5.2.2
$ /data/server/php/php72/bin/phpize
$ ./configure --with-php-config=/data/server/php/php56/bin/php-config
$ make && make install
```



> 上面之所以要指定 `--with-php-config`，是因为我们系统之中可能会安装多个 `PHP` 版本。此时，可能会造成编译混乱的情况发生。直接指定可以避免一些不可预料的问题。



关于：`./configure` 更多的参数请参见：https://www.php.net/manual/zh/install.pecl.php-config.php



#### 2.2）PHP 内置扩展安装

有时候我们可能会在安装 `PHP` 的时候禁用了 `PHP` 内置的扩展。可能在 `pecl` 找不到这个扩展安装。或者，我们就是想安装原装的扩展。此时，我们可以直接把当初的 `PHP` 源码解压进入 `ext` 目录查找对应的扩展。比如，我的 `PHP7.2.30` 版本自带的内置扩展有如下这些：

```
drwxrwxr-x.  5 root root  187 4月  29 09:04 bcmath
drwxrwxr-x.  3 root root  157 4月  14 23:37 bz2
drwxrwxr-x.  4 root root 4096 4月  29 09:04 calendar
drwxrwxr-x.  3 root root 4096 4月  14 23:37 com_dotnet
drwxrwxr-x.  4 root root  183 4月  29 09:04 ctype
drwxrwxr-x.  4 root root 4096 4月  29 09:04 curl
drwxrwxr-x.  5 root root  179 4月  29 09:03 date
drwxrwxr-x.  6 root root 4096 4月  14 23:37 dba
drwxrwxr-x.  5 root root 4096 4月  29 09:05 dom
drwxrwxr-x.  4 root root  138 4月  14 23:37 enchant
drwxrwxr-x.  3 root root  171 4月  14 23:37 exif
-rwxrwxr-x.  1 root root 9415 4月  14 23:37 ext_skel
-rw-rw-r--.  1 root root 1165 4月  14 23:37 ext_skel_win32.php
drwxrwxr-x.  5 root root 4096 4月  29 09:05 fileinfo
drwxrwxr-x.  5 root root 4096 4月  29 09:05 filter
drwxrwxr-x.  4 root root  223 4月  29 09:05 ftp
drwxrwxr-x.  5 root root  236 4月  29 09:05 gd
drwxrwxr-x.  4 root root  155 4月  29 09:05 gettext
drwxrwxr-x.  3 root root  167 4月  14 23:37 gmp
drwxrwxr-x.  5 root root 4096 4月  29 09:05 hash
drwxrwxr-x.  4 root root 4096 4月  29 09:05 iconv
drwxrwxr-x.  3 root root  133 4月  14 23:37 imap
drwxrwxr-x.  3 root root  249 4月  14 23:37 interbase
drwxrwxr-x. 21 root root 4096 4月  14 23:37 intl
drwxrwxr-x.  4 root root 4096 4月  29 09:05 json
drwxrwxr-x.  3 root root  145 4月  14 23:37 ldap
drwxrwxr-x.  4 root root  175 4月  29 09:03 libxml
drwxrwxr-x.  7 root root 4096 4月  29 09:06 mbstring
drwxrwxr-x.  4 root root 4096 4月  29 09:06 mysqli
drwxrwxr-x.  3 root root 4096 4月  29 09:08 mysqlnd
drwxrwxr-x.  3 root root 4096 4月  14 23:37 oci8
drwxrwxr-x.  3 root root  172 4月  14 23:37 odbc
drwxrwxr-x.  5 root root 4096 4月  29 09:03 opcache
drwxrwxr-x.  4 root root  219 4月  29 09:03 openssl
drwxrwxr-x.  4 root root  265 4月  29 09:06 pcntl
drwxrwxr-x.  5 root root  215 4月  29 09:04 pcre
drwxrwxr-x.  4 root root 4096 4月  29 09:06 pdo
drwxrwxr-x.  3 root root  214 4月  14 23:37 pdo_dblib
drwxrwxr-x.  3 root root  220 4月  14 23:37 pdo_firebird
drwxrwxr-x.  4 root root 4096 4月  29 09:06 pdo_mysql
drwxrwxr-x.  3 root root  195 4月  14 23:37 pdo_oci
drwxrwxr-x.  3 root root  215 4月  14 23:37 pdo_odbc
drwxrwxr-x.  3 root root  205 4月  14 23:37 pdo_pgsql
drwxrwxr-x.  4 root root 4096 4月  29 09:06 pdo_sqlite
drwxrwxr-x.  3 root root  157 4月  14 23:37 pgsql
drwxrwxr-x.  5 root root 4096 4月  29 09:10 phar
drwxrwxr-x.  4 root root  148 4月  29 09:06 posix
drwxrwxr-x.  3 root root  119 4月  14 23:37 pspell
drwxrwxr-x.  3 root root  175 4月  14 23:37 readline
drwxrwxr-x.  3 root root  105 4月  14 23:37 recode
drwxrwxr-x.  4 root root  179 4月  29 09:06 reflection
drwxrwxr-x.  4 root root 4096 4月  29 09:06 session
drwxrwxr-x.  3 root root  156 4月  14 23:37 shmop
drwxrwxr-x.  5 root root  277 4月  29 09:06 simplexml
drwxrwxr-x.  3 root root  134 4月  14 23:37 skeleton
drwxrwxr-x.  3 root root  101 4月  14 23:37 snmp
drwxrwxr-x.  5 root root 4096 4月  29 09:07 soap
drwxrwxr-x.  4 root root 4096 4月  29 09:07 sockets
drwxrwxr-x.  3 root root  149 4月  14 23:37 sodium
drwxrwxr-x.  6 root root 4096 4月  29 09:07 spl
drwxrwxr-x.  5 root root  202 4月  29 09:04 sqlite3
drwxrwxr-x.  5 root root 8192 4月  29 09:07 standard
drwxrwxr-x.  3 root root  108 4月  14 23:37 sysvmsg
drwxrwxr-x.  3 root root  108 4月  14 23:37 sysvsem
drwxrwxr-x.  3 root root  126 4月  14 23:37 sysvshm
drwxrwxr-x.  4 root root  170 4月  14 23:37 tidy
drwxrwxr-x.  4 root root 4096 4月  29 09:07 tokenizer
drwxrwxr-x.  3 root root  142 4月  14 23:37 wddx
drwxrwxr-x.  4 root root  244 4月  29 09:07 xml
drwxrwxr-x.  5 root root  236 4月  29 09:07 xmlreader
drwxrwxr-x.  4 root root  150 4月  14 23:37 xmlrpc
drwxrwxr-x.  5 root root  242 4月  29 09:07 xmlwriter
drwxrwxr-x.  3 root root  142 4月  14 23:37 xsl
drwxrwxr-x.  2 root root   73 4月  14 23:37 zend_test
drwxrwxr-x.  6 root root  273 4月  29 09:08 zip
drwxrwxr-x.  4 root root 4096 4月  29 09:04 zlib
```

当然 `PHP` 大部分扩展都默认开发编译的。部分内置扩展需要在编译的时候使用类似 `--enable-sockets` 的方式开启。

**安装内置 `fileinfo` 扩展为例**

```
$ cd ./ext/fileinfo
$ /data/server/php/php72/bin/phpize
$ ./configure --with-php-config=/data/server/php/php72/bin/php-config
$ make && make install
```

#### 2.3）`pecl` 命令安装扩展

`PECL` 的全称是 `The PHP Extension Community Library`。是一个开放的并通过 `PEAR(PHP Extension and Application Repository, PHP 扩展和应用仓库)` 打包格式来安装的 `PHP` 扩展仓库。通过 `PEAR` 的 `Package Manager` 的安装管理方式，可以对 `PECL` 模块进行下载和安装。

`PECL` 命令在 `PHP` 安装的时候，就已经在安装目录下的 `bin` 目录中。

通过 `PECL` 命令只能安装在 `pecl.php.net` 网站发布的扩展。



##### 2.3.1）安装

```
$ /data/server/php/php72/bin/pecl install yaf
```

对，安装就是这么简单。

默认情况下 `pecl` 命令会下载最新发布的稳定版本来安装。



安装指定版本的扩展。

稳定版本那么多，有可能最新的版本存在 BUG，我们可能就需要安装到指定版本去。

```
$ /data/server/php/php72/bin/pecl install yaf-3.1.3
```



##### 2.3.2）卸载

有安装，就是卸载。

```
$ /data/server/php/php72/bin/pecl uninstall yaf
```



##### 2.3.3）更新

假如，有新的稳定版扩展发布了。我们也可以通过 `pecl` 命令进行更新。

```
$ /data/server/php/php72/bin/pecl upgrade yaf
```

如果您想更新所有的扩展？

```
$ /data/server/php/php72/bin/pecl upgrade-all
```

不过这种方式不怎么推荐。还有可能出现难以预料的错误。笔者暂时未找到原因。后续补充更新。可能是由于我们通过不同的方式编译安装了不同的包所致吧。



##### 2.3.4）查找

查询 `pecl.php.net` 官网是否有我们想要的包。以及包的版本及稳定性。

比如，我们想查询是否有 `mongodb` 包。

```
$ /data/server/php/php72/bin/pecl search mongodb
WARNING: channel "pecl.php.net" has updated its protocols, use "pecl channel-update pecl.php.net" to update
Retrieving data...0%
Matched packages, channel pecl.php.net:
=======================================
Package Stable/(Latest)   Local
mongodb 1.8.0beta1 (beta)       MongoDB driver for PHP
```

上面出现了一个警告：`WARNING: channel "pecl.php.net" has updated its protocols, use "pecl channel-update pecl.php.net" to update` 。

大致意思就是协议发生了变更，需要我们执行 `pecl channel-update pecl.php.net` 进行更新。

```
$ /data/server/php/php72/bin/pecl channel-update pecl.php.net
Updating channel "pecl.php.net"
Update of Channel "pecl.php.net" succeeded
```



##### 2.3.5）列出已安装扩展

我们总会想查询我们已经通过 `pecl` 命令安装了哪些扩展。可以通过如下命令操作。

```
$ /data/server/php/php72/bin/pecl list
Installed packages, channel pecl.php.net:
=========================================
Package Version State
yaf     3.1.4   stable
```

> 注：`pecl list` 命令只能查询通过 `pecl` 命令安装的扩展。无法列出其他方式安装的库。这个要特别注意。



##### 2.3.6）查看扩展信息

扩展安装成功之后，我们可能想进一步了解它的秘密。

```
$ /data/server/php/php72/bin/pecl info yaf
About pecl.php.net/yaf-3.1.4
============================
Release Type          PECL-style PHP extension (source code)
Name                  yaf
Channel               pecl.php.net
Summary               PHP Framework in PHP extension
Description           Yaf is a PHP framework similar to zend
                      framework, which is written in c and built as
                      PHP extension
Maintainers           Xinchen Hui <laruence@php.net> (lead)
Release Date          2020-04-01 03:20:52
Release Version       3.1.4 (stable)
API Version           3.1.4 (stable)
License               PHP (http://www.php.net/license)
Release Notes         - Fixed issue #469 (treat autocontroller as
                      Controller mistakenly)
                      - Fixed issue #468 (abort if same key assigned
                      to view)
                      - minor optimization to avoding memory
                      allocations
Required Dependencies PHP version 7.0.0
                      PEAR installer version 1.4.0 or newer
package.xml version   2.0
Last Modified         2020-05-06 07:32
Previous Installed    - None -
Version
```

以上只能查看本地已安装的扩展的信息。但是，有时候我们可能想事先了解之后再安装。`pecl` 也贴心准备了一个查看官网扩展包信息的命令。

```
$ /data/server/php/php72/bin/pecl remote-info yaf
Package details:
================
Latest      2.5.4
Installed   - no -
Package     event
License     PHP
Category    Networking
Summary     Provides interface to libevent library
Description This is an extension to efficiently schedule
            I/O, time and signal based
              events using the best I/O notification
            mechanism available for specific platform.
              This is a port of libevent to the PHP
            infrastructure.

              Version 1.0.0 introduces:
              * new OO API breaking backwards compatibility
              * support of libevent 2+ including HTTP, DNS,
            OpenSSL and the event listener.

              Note, event-1.0.0 and greater is not
            compatible with previous versions.
```



##### 2.3.7）其他

`pecl` 还提供了其他参数。常用的也就那么几个。还有一些打包的命令。大家可以直接执行 `pecl` 命令查看。

```
$ /data/server/php/php72/bin/pecl
Commands:
build                  Build an Extension From C Source
bundle                 Unpacks a Pecl Package
channel-add            Add a Channel
channel-alias          Specify an alias to a channel name
channel-delete         Remove a Channel From the List
channel-discover       Initialize a Channel from its server
channel-info           Retrieve Information on a Channel
channel-login          Connects and authenticates to remote channel server
channel-logout         Logs out from the remote channel server
channel-update         Update an Existing Channel
clear-cache            Clear Web Services Cache
config-create          Create a Default configuration file
config-get             Show One Setting
config-help            Show Information About Setting
config-set             Change Setting
config-show            Show All Settings
convert                Convert a package.xml 1.0 to package.xml 2.0 format
cvsdiff                Run a "cvs diff" for all files in a package
cvstag                 Set CVS Release Tag
download               Download Package
download-all           Downloads each available package from the default channel
info                   Display information about a package
install                Install Package
list                   List Installed Packages In The Default Channel
list-all               List All Packages
list-channels          List Available Channels
list-files             List Files In Installed Package
list-upgrades          List Available Upgrades
login                  Connects and authenticates to remote server [Deprecated in favor of channel-login]
logout                 Logs out from the remote server [Deprecated in favor of channel-logout]
makerpm                Builds an RPM spec file from a PEAR package
package                Build Package
package-dependencies   Show package dependencies
package-validate       Validate Package Consistency
pickle                 Build PECL Package
remote-info            Information About Remote Packages
remote-list            List Remote Packages
run-scripts            Run Post-Install Scripts bundled with a package
run-tests              Run Regression Tests
search                 Search remote package database
shell-test             Shell Script Test
sign                   Sign a package distribution file
svntag                 Set SVN Release Tag
uninstall              Un-install Package
update-channels        Update the Channel List
upgrade                Upgrade Package
upgrade-all            Upgrade All Packages [Deprecated in favor of calling upgrade with no parameters]
Usage: pecl [options] command [command-options] <parameters>
Type "pecl help options" to list all options.
Type "pecl help shortcuts" to list all command shortcuts.
Type "pecl help version" or "pecl version" to list version information.
Type "pecl help <command>" to get the help for the specified command.
```



#### 2.4）配置 `php.ini` 以加载扩展

我们的扩展安装成功了，现在就是要让 `PHP` 加载我们的扩展。扩展要能加载成功，必须修改 `php.ini` 配置文件。而配置文件的修改是很讲究的。

以 `redis` 扩展为例，在 `php.ini` 配置文件中找到包含 `extension=` 字样的扩展列表位置。如下：

```
;extension=bz2
;extension=curl
;extension=fileinfo
;extension=gd2
;extension=gettext
;extension=gmp
;extension=intl
;extension=imap
;extension=interbase
;extension=ldap
;extension=mbstring
;extension=exif      ; Must be after mbstring as it depends on it
;extension=mysqli
;extension=oci8_12c  ; Use with Oracle Database 12c Instant Client
;extension=odbc
;extension=openssl
;extension=pdo_firebird
;extension=pdo_mysql
;extension=pdo_oci
;extension=pdo_odbc
;extension=pdo_pgsql
;extension=pdo_sqlite
;extension=pgsql
;extension=shmop
```

在这些配置文件后面添加如下配置：

```
extension=redis.so
```

那么，我们为什么不像有的文章说的那样直接把上述配置添加到 `php.ini` 文件末尾呢？

那是因为在 `php.ini` 文件当中有节的概念。比如，`session` 的配置就放在 `[session]` 这个节下面，那么文件末尾就会把扩展附加到其它节上了。这就要看这个节是哪个名称了。



当然，大多数版本放在文件末尾是可以生效的。但是，为了规范还是请放到上面推荐的位置。



> 扩展的加载方式还有另外一种：通过 `dl()` 函数加载。通过 `dl()` 方式加载扩展，可以实现按需加载。提高 `PHP` 启动的速度。 但是，极其不推荐。这里仅作扩展了解。



#### 2.5）验证扩展是否安装成功

**方式一:**

```
$ /data/server/php/php72/bin/php -m|grep redis
redis
```

输出 `redis` 字样就说明已经安装成功。如果没有就是安装失败了。

**方式二：**

```
$ /data/server/php/php72/bin/php --ri redis
redis

Redis Support => enabled
Redis Version => 5.2.1
Redis Sentinel Version => 0.1
Available serializers => php, json

Directive => Local Value => Master Value
redis.arrays.algorithm => no value => no value
redis.arrays.auth => no value => no value
redis.arrays.autorehash => 0 => 0
redis.arrays.connecttimeout => 0 => 0
redis.arrays.distributor => no value => no value
redis.arrays.functions => no value => no value
redis.arrays.hosts => no value => no value
redis.arrays.index => 0 => 0
redis.arrays.lazyconnect => 0 => 0
redis.arrays.names => no value => no value
redis.arrays.pconnect => 0 => 0
redis.arrays.previous => no value => no value
redis.arrays.readtimeout => 0 => 0
redis.arrays.retryinterval => 0 => 0
redis.arrays.consistent => 0 => 0
redis.clusters.cache_slots => 0 => 0
redis.clusters.auth => no value => no value
redis.clusters.persistent => 0 => 0
redis.clusters.read_timeout => 0 => 0
redis.clusters.seeds => no value => no value
redis.clusters.timeout => 0 => 0
redis.pconnect.pooling_enabled => 1 => 1
redis.pconnect.connection_limit => 0 => 0
redis.session.locking_enabled => 0 => 0
redis.session.lock_expire => 0 => 0
redis.session.lock_retries => 10 => 10
redis.session.lock_wait_time => 2000 => 2000
```

说明咱们安装成功了。

后面输出的那一大段信息是 `redis` 扩展的配置信息。如果您想要更改这些配置，可以直接在`extension=redis.so`后台添加如下修改：

```
extension=redis.so
redis.session.lock_wait_time = 20000
```

改之前一定要明白其配置的含义。避免盲目修改导致的错误。

### 3）知识拓展

#### 3.1）扩展编译时参数介绍

扩展下载安装回来的时候，我们有一个可选顶参数指定 `php-config` 的位置。如下：

```
$ ./configure --with-php-config=/data/server/php/php72/bin/php-config
```

但是，编译参数并不仅仅只有这么一个。还有还好几个有用的。通常我们用得少，所以给予的关注也就很少。可以通过如下官网链接再进行拓展了解。

https://www.php.net/manual/zh/install.pecl.php-config.php



3.2）将 `PECL` 扩展库静态编译入 `PHP`







