# 0x01.2 PHP 5.6 安装

`PHP 5.6` 未来很长一段时间内会有一定存量继续使用。所以，我们今天会着重来讲一讲 `PHP 5.6` 的安装。即使如此，也尽量希望所有的开发者将服务器 `PHP` 版本升级到最新的 `PHP 7.2+` 版本，兼容最新特性。

### 1) 注意事项

> 当前（2020.04.28）最新的 `PHP 5.6` 版本为 `PHP 5.6.40`。所以，以下的示例均以此版本为基础进行安装。

在安装之前我们已经源码编译安装（PHP 安装准备工作小节）了 `libiconv`、`zlib`、`libpng`、`libjpeg`、`freetype2`、`curl` 库。但是，我们还开启了其他的库。所以，还需要安装其他系统类库。这里以 `CentOS` 系统为例。

```
$ yum install -y libxml2 libxml2-devel openssl openssl-devel libpng-devel freetype-devel
```

> 我们这里只安装了 libpng-devel。而没有安装 libpng。是因为 libpng 我们已经手动源码编译安装了。只安装 libpng-devel 来提供 php 编译时调用的头文件和链接库。

> 再次重申：手动源码编译的好处在于，可以避免 yum 源版本类库太旧引发的 BUG。重要的或正式环境的系统类库必须手动编译安装。



因为我们安装的时候激活了 `libxml`、`openssl` 扩展。由于手动编译麻烦，我就直接使用 `yum` 安装了这两个类库。但是，当前 `openssl` 通过 `yum` 安装的时候是 `1.0.2k` 版本。而官网最新版本是 `1.1.1g`。而新版本修复了好几个 `BUG`。所以，使用 `yum` 虽然方便，但是生产环境最好保持使用最新最稳定的版本才是正道。



### 2）编译安装

```
$ wget https://www.php.net/distributions/php-5.6.40.tar.gz
$ tar zxvf php-5.6.40.tar.gz
$ cd php-5.6.40
$ ./configure --prefix=/data/server/php/php56 \
--enable-opcache \
--with-config-file-path=/data/server/php/php56/etc \
--with-mysqli=mysqlnd \
--with-pdo-mysql=mysqlnd \
--enable-fpm \
--enable-static \
--enable-inline-optimization \
--enable-sockets \
--enable-pcntl \
--with-gettext \
--enable-zip \
--enable-calendar \
--enable-bcmath \
--enable-soap \
--with-zlib-dir=/usr/local \
--enable-phpdbg \
--with-iconv=/usr/local \
--with-gd \
--enable-libxml \
--enable-mbstring \
--with-curl \
--enable-ftp \
--with-freetype-dir=/usr/local/freetype2.10.0 \
--with-jpeg-dir=/usr/local/jpeg.9d \
--with-png-dir=/usr/local/libpng.1.6.37 \
--with-curl=/usr/local/curl7.69.1 \
--disable-ipv6 \
--disable-debug \
--with-openssl \
--disable-maintainer-zts
$ make ZEND_EXTRA_LIBS='-liconv'
$ make install
```



到此，我们的 `PHP 5.6` 就已经安装完了。

### 3）php.ini 配置

现在，我们来设置 `php.ini` 与 `php-fpm`。

默认情况下，php.ini 配置文件不会自动生成到我们指定的配置目录：`/data/server/php/php56/etc` 。那么，这个 php.ini 文件在哪呢？

这个文件在源码目录里面。

```
......
-rw-r--r--.  1 1000 1000   73654 1月   9 2019 php.ini-development
-rw-r--r--.  1 1000 1000   73685 1月   9 2019 php.ini-production
......
```

如果是正式环境推荐将 `php.ini-production` 复制到配置目录。开发环境就将 `php.ini-development` 复制到配置目录。我们以配置正式环境为例。

```ini
cp ./php.ini-production /data/server/php/php56/etc/php.ini
```

> 开发配置与正式配置的最大区别在于日志的显示。开发配置日志会直接输出到页面。



#### 3.1）设置时区

找到 `date.timezone` 这行配置，将前面的注释去掉。设置为如下这样：

```ini
date.timezone = PRC
```



#### 3.2）设置 POST 数据大小

默认情况下 `php.ini` 限制表单提交的数据大小为 `8M`。在特殊环境下，可能会影响业务的正常运转。找到 `post_max_size` 并修改为 `64M`。至于多少合适，请以实际的业务来设定。

```ini
post_max_size = 64M
```



#### 3.3）设置文件上传大小

默认情况下 `php.ini` 限制文件上传的大小为 `2M`。在当今这个世界 `2M` 完全是不够的。找到 `post_max_size` 并修改为 `64M`。至于多少合适，要以实际的业务来设定。

```ini
upload_max_filesize = 64M
```



#### 3.4）设置脚本最大执行时间

默认情况下 `max_execution_time` 的值为 30 秒。有极端情况下，30 秒很容易超出实际业务的时长，造成业务的不稳定。

```ini
max_execution_time = 60
```

#### 3.5）设置错误日志路径

当 `PHP` 脚本运行报错的时候写入的日志路径。

```ini
error_log = /data/log/php/php56/php_errors.log
```

#### 3.6）设置扩展目录

`PHP` 除了内置的扩展之外，我们还可以根据项目的实际需求安装第三方的扩展。而这些扩展编译之后都会生成 `so` 文件在特定的目录。而这个目录与 `PHP` 的版本密切相关。

比如，常见的第三方扩展有：`redis`、`memcache`、`yaf` 等。

```ini
extension_dir = "/data/server/php/php56/lib/php/extensions/no-debug-non-zts-20131226/"
```

一定要去掉这一行配置前面的注释信息。



### 4）php-fpm.conf 配置

`php-fpm.ini` 配置文件是我们在编译时指定 `--enable-fpm` 参数。则会把 `php-fpm.conf.default` 生成到 `/data/server/php/php56/etc` 配置目录下。

#### 4.1）生成php-fpm.conf

```ini
cp /data/server/php/php56/etc/php-fpm.conf.default /data/server/php/php56/etc/php-fpm.conf
```

#### 4.2）设置 php-fpm 日志路径

找到 `error_log` 修改为如下（记得将前面的注释去掉）：

```ini
error_log = /data/log/php/php56/php-fpm.log
```

#### 4.3）设置 php-fpm 慢请求日志路径

找到 `slowlog` 修改为如下(记得将前面的注释去掉)：

```ini
slowlog = /data/log/php/php56/$pool.log.slow
```

#### 4.4）设置用户与用户组

用户与用户组是 `php-fpm` 以哪个用户身份来运行 `php`。通常我们会在 `linux` 系统创建一个 `www` 的用户与 `www` 的用户组。如果您没有创建，请现在立即创建它。

创建方式：

```shell
groupadd www
useradd -g www -M -d /data/www -s /usr/sbin/nologin www &> /dev/null
```

> 指定该用户的目录为：/data/www ，后续该目录用作 web 映射的目录。



找到下述位置并修改：

```ini
user = www
group = www
```

#### 4.5）设置 php-fpm pid

`pid` 是指 `php-fpm` 父进程存放进程 `id` 的位置配置。记得将前面的注释去掉。

```ini
pid = run/php-fpm.pid
```

#### 4.6）设置 php-fpm 进程启动时的数量

这个启动数量请根据实际业务与服务器配置来决定。给一个大概的参考值：8G（系统总内存）- 1G（系统基础内存消耗）= 7G / 25M = 需要设置的最高理论上限数量。当然，进程越多都 CPU 核数要求也越高。

```ini
pm.start_servers = 20
```

#### 4.7）设置 php-fpm 子进程可启动的最大数量

```ini
pm.max_children = 100
```

#### 4.8）设置 php-fpm 空闲进程最大值

```ini
pm.max_spare_servers = 50
```

#### 4.9）设置 php-fpm 空闲进程最小值

```ini
pm.min_spare_servers = 20
```

#### 4.10）listen 相关配置设置

找到下面这组配置修改为如下：

```ini
listen.owner = www
listen.group = www
listen.mode = 0660
```

#### 4.11）错误捕获

重定向运行过程中的 `stdout` 和 `stderr` 到主要的错误日志文件中. 如果没有设置, `stdout` 和 `stderr` 将会根据`FastCGI` 的规则被重定向到 `/dev/null` 。 默认值: 空。

```
catch_workers_output = yes
```



#### 4.12）最终修改的配置汇总

```ini
[global]
pid = run/php-fpm.pid
error_log = /data/log/php/php56/php-fpm.log
[www]
user = www
group = www
pm = dynamic
listen = 127.0.0.1:9000
listen.owner = www
listen.group = www
listen.mode = 0660
pm.max_children = 50
pm.start_servers = 20
pm.max_spare_servers = 50
pm.min_spare_servers = 20
pm.max_requests = 500
catch_workers_output = yes
request_slowlog_timeout = 3s
slowlog = /data/log/php/php56/$pool.log.slow
```

> 关于 PHP-FPM 各项参数的说明，我们会在后面有单独的章节进行详情介绍。大家可以移步前去查看。



### 5）设置系统启动

`PHP` 已经为我们管理 `php-fpm` 服务已经准备好了管理脚本。当我们编译安装成功之后，同时这个 `sapi/fpm` 目录也会被编译。会生成一个 `init.d.php-fpm` 的脚本。

复制 `sapi/fpm/php-fpm` 脚本到 `/etc/init.d/` 目录。

在已编译后的 `PHP` 源码目录下执行复制命令：

```
$ cp ./sapi/fpm/init.d.php-fpm /etc/init.d/php56-fpm
```

这个地方直接命名为 `php56-fpm`。是考虑到后续可能还会与其他版本的共存。所以，并没有直接命名为`php-fpm`。

默认情况下 `php56-fpm` 并没有可执行的权限。所以，我们还要给它增加一个可执行权限：

```
$ chmod 0755 /etc/init.d/php72-fpm
```



#### 5.1）脚本方式管理

**启动：**

```
$ /etc/init.d/php56-fpm start
```

**重启：**

```
$ /etc/init.d/php56-fpm restart
```

**停止：**

```
$ /etc/init.d/php56-fpm stop
```

**重载(平滑重启)：**

```
$ /etc/init.d/php56-fpm reload
```

**状态查看**

```
$ /etc/init.d/php56-fpm status
```

**强制退出**

```
$ /etc/init.d/php56-fpm force-quit
```



#### 5.2）service 命令管理

在低版本的 Linux 系统之中，该命令并不支持。

**启动：**

```
$ service php56-fpm start
```

**重启：**

```
$ service php56-fpm restart
```

**停止：**

```
$ service php56-fpm stop
```

**状态查看：**

```
$ service php56-fpm status
```

**重载（平滑重启）:**

```
$ service php56-fpm reload
```

**强制退出：**

```
$ service php56-fpm force-quit
```



### 6）软件备份

上面我们安装编译了 `PHP` 依赖的系统类库，还有 `PHP` 本身。假如有一天，我们需要重新编译某个扩展或开启 `PHP` 某个配置。那么，再去下载指定的版本可能会存在已经不维护找不到下载地址的情况。

所以，我们要将其备份起来。

将所有压缩包全部移动到 `/data/serbak` 目录。如果可以的话，最好创建一个子目录，目录名称带上关键信息。比如：`20200429-php5.6.40`。

> 由于我们主要是安装 PHP。所以，最好把解压编译后的目录一起复制过去是最好的了。

### 7）总结

整个安装流程大致如此。即使是 `PHP 5.3`、`PHP 5.4`、`PHP 5.5` 都是如此。它们的不同之处，我总结了几点：

```
1) 编译时某些配置参数去掉或新增。
2) 某些扩展不再支持。比如, PHP7.1+ 之后的版本不再支持 mcrypt 版本。还有 mysql 扩展。
3) 依赖的系统类库有最低版本的要求。比如，zlib 扩展在 PHP 5.4.0 之前 >= 1.0.9,之后 >= 1.2.0.4。
4) 某些扩展已经内置。比如，mysqlnd 在 PHP 5.3.0 之前没有内置。必须手动编译安装。
```

**安装流程图：**

![](..\images\0x01-php-flow.png)









