# 0x01 PHP 安装

> PHP 软件的安装是每一位 PHP 开发者必须掌握的一个技能。很多人可能在 windows 系统下安装 PHP 软件。觉得安装 PHP 是非常容易的一件事情。然而在 Linux 环境下安装 PHP 却并不容易。所以，我们此系列教程如果没有特别说明，全部在 CentOS7 for linux 环境下操作。

## 一、准备工作
安装 PHP 之前，我们要做一些准备工作。不管是哪个 PHP 版本安装都能用得到。

### 1) PHP 安装目录
Web 服务器通常并非仅仅只安装 PHP。也有可能会安装 MySQL、Nginx、Redis 等软件。那么，将这些软件统统放在一个指定的目录下，就非常方便后续的管理了。

```
; 所有 Web Server 软件安装目录
/data/server

; 所有 Web Server 软件日志目录
/data/log

; 所有 Web Server 下载目录(备份)
/data/serback
```

所以，按照如上目录划分。我们的 PHP 的安装目录应该为：`/data/server/php`。如果是安装 `PHP5.3`, 则目录为：`/data/server/php/php53`。

### 2）PHP 依赖库安装
PHP 很多的内置扩展或开发中常用的扩展会依赖 Linux 系统底层库的支持。所以，要想使用这些功能，需要提前安装这些库。

#### 2.1) 安装 libiconv 库

为什么要安装这个库呢？是因为 php 里面的 `iconv` 打头的函数依赖这个库实现它的功能。更直白一点说：所有这些 `iconv` 打头的函数直接包装了 `Linux` 系统底层的 `libiconv` 库实现其功能。

**libiconv官网：http://www.gnu.org/software/libiconv/**

下载&安装：

```
$ wget https://ftp.gnu.org/pub/gnu/libiconv/libiconv-1.16.tar.gz
$ tar zxvf libiconv-1.16.tar.gz
$ cd libiconv-1.16
$ ./configure --prefix=/usr/local
$ make && make install
```

这样就算安装成功了。因为 `libiconv` 会保持更新的缘故，可能上面的下载链接可能会发生变化。大家此时可以登录官网获取最新的下载链接即可。

虽然在 `CentOS` 系统可以通过 `yum install -y libiconv libiconv-devel` 安装这个库。但是，有时候 `yum` 源版本太低了。低版本可能存在一些 `BUG`。所以，可以根据自己的特点进行安装。下面的源码安装方式依然如此。



#### 2.2）安装 zlib 库

`PHP` 官方（https://www.php.net/manual/zh/zlib.requirements.php）文档说了。从 `PHP 5.4` 开始必须使用 zlib 库，并且 `zlib` 库的版本必须大于 等于`1.2.0.4 `。

那么这个库又有什么说法呢？因为 https://www.php.net/manual/zh/book.zlib.php 链接里面对应的方法全部依赖这个底层库。

官网：http://www.zlib.net/

下载&&安装：

```
$ wget http://www.zlib.net/zlib-1.2.11.tar.gz
$ tar zxvf zlib-1.2.11.tar.gz
$ cd zlib-1.2.11
$ ./configure --prefix=/usr/local
$ make CFLAGS=-fpic
$ make install
```

由于 `zlib` 会保持更新的缘故，上面的链接若失效。请访问官网，找到最新的下载链接即可。



#### 2.3）安装 freetype2 库

没错。是 `freetype2` 库。因为 `freetype 1` 版本的库从 `PHP 5.3.0` 开始，不再提供对 `FreeType 1.x` 的支持。所以，我们不管是 `PHP 5.3` 以前还是以后直接使用 `freetype2` 版本即可。

那么这个库有什么作用呢？

在 PHP 官方的文档（https://www.php.net/manual/zh/image.requirements.php）中已经说了：当我们使用 GD 库来处理图片的时候，希望增强 GD 库来使用不同的字体时会需要这个类库。

我们经常会默认使用 GD 库来生成图形验证码、裁剪图片、图片打水印等功能。所以，这个类库是相当的有用了。

官网：https://www.freetype.org/

下载&&安装：

```
$ wget https://download.savannah.gnu.org/releases/freetype/freetype-2.10.0.tar.gz
$ tar zxvf freetype-2.10.0.tar.gz
$ cd freetype-2.10.0
$ ./configure --prefix=/usr/local/freetype.2.10.0
$ make && make install
```

最好安装最新的版本。https://download.savannah.gnu.org/releases/freetype/ 这个地址包含了新老版本的库。大家可以根据自己的需求去选择。



#### 2.4）安装 libpng 库

这个库应该从名称就知道它的用处了吧？就是在处理 `png` 图片的时候，会用到这个库。`GD` 库也会用到它的噢。

官网：http://www.libpng.org/pub/png/libpng.html

下载&&安装：

下载地址：http://prdownloads.sourceforge.net/libpng/libpng-1.6.37.tar.gz?download 

由于这个地址必须通过在网页里面下载，无法通过 `wget` 下载。所以，大家先下载到本地上传到服务器再安装。

```
$ tar zxvf libpng-1.6.37.tar.gz
$ cd libpng-1.6.37
$ ./configure --prefix=/usr/local/libpng.1.6.37
$ make CFLAGS=-fpic
$ make install
```



#### 2.5）安装 libevent 库

这个库是一个非常知名且强大的事件库。`PHP` 里面所有以 `event_` 开头的函数都是调用它提供的功能实现。所以，如果会用到这个库的话，最好安装它。

官网：http://libevent.org/

下载&&安装：

```
$ wget https://github.com/libevent/libevent/releases/download/release-2.1.11-stable/libevent-2.1.11-stable.tar.gz
$ tar zxvf libevent-2.1.11-stable.tar.gz
$ cd libevent-2.1.11-stable
$ ./configure --prefix=/usr/local/libevent-2.1.11
$ make && make install
```

> 当然，这个是 Linux 底层的事件库。但是，要在 PHP 里面使用的话，还需要安装 PHP C 扩展：libevent 或 event。



#### 2.6）安装 libcurl 库

我们在开发过程中，经常会使用 `curl` 来调用其他平台提供的接口。所以，我们也需要安装 `libcurl` 库。通常这个库已经在 `linux` 系统之中已经安装了。

如果没有安装怎么办?

如果您的系统是 `CentOS` 系统：

```
$ yum install libcurl-devel -y
```

由于 `yum` 安装的库更新较慢，可能存在一些特定的 `BUG`，或不支持某些新版本的特性。此时，我们可能更希望自己手动编译安装：

官网下载地址：https://curl.haxx.se/download.html

```
$ wget https://curl.haxx.se/download/curl-7.69.1.tar.gz
$ tar zxvf curl-7.69.1.tar.gz
$ cd curl-7.69.1
$ ./configure --prefix=/usr/local/curl
$ make && make install
```

> PHP 需要使用 7.10.5 或更高版本的 libcurl。



#### 2.7）其他方式安装类库

如果你的系统是 CentOS 的话，完全不用上面那么麻烦。直接可以通过 `yum` 来安装。只不过，有些库在 `yum` 源中版本太低，导致我们不得不使用源码安装。如果安装过程中，出现版本太低的情况。大家选择手动编译安装。

否则，可以像下面这样安装其他类库：

```
$ sudo -s
$ LANG=C
$ yum install -y gcc gcc-c++ autoconf libxml2 libxml2-devel libjpeg libjpeg-devel libjpeg-turbo-devel libiconv-devel freetype freetype-devel libpng libpng-devel gd-devel libcurl-devel libxslt-devel zlib zlib-devel glibc glibc-devel glib2 glib2-devel curl curl-devel openssl openssl-devel
```

这里已经安装了上面全部的库。所以，使用 CentOS 系统，在安装这些库的时候非常之方便。如果不是 CentOS 系统的话，就要一个一个编译安装了。当然，其他的 Linux 系统也有自己的类库管理工具。



> 注：为了防止类库安装之后没有被系统加载。我们可以手动执行这个命令立即生效：`/sbin/ldconfig`



### 3 ./configure 及参数介绍

在 Linux 系统编译安装软件的时候，几乎都会在源码根目录运行 `./configure` 命令。

那么它到底是干嘛的呢？

> ./configure 是用来检测您的目标安装平台的目标特征的。

上面这句话如何理解呢？比如，我们安装的时候依赖某个类库，如果不是安装在常用的位置。那么，此时可能会通过该命令进行指定。还有就是当一个类库有不同版本共存时。而软件依赖的是低版本的。可能此时就需要特殊指定低版本库的路径。除此之外，还能指定软件编译安装到哪个目录。

我们现在要讲的，就是在 `Linux` 安装时，`./configure` 可用的配置参数。

示例：

```
./configure --prefix=/data/server/php/php72 \
--enable-opcache \
--with-config-file-path=/data/server/php/php72/etc \
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
--with-zlib \
--with-iconv=/usr/local \
--with-gd \
--enable-libxml \
--enable-mbstring \
--with-curl \
--enable-ftp \
--with-freetype-dir=/usr/local/freetype.2.1.10 \
--with-jpeg-dir=/usr/local/jpeg.9 \
--with-png-dir=/usr/local/libpng.1.6.36 \
--disable-ipv6 \
--disable-debug \
--with-openssl \
--disable-maintainer-zts
```



#### 3.1）--prefix

设置 `PHP` 的安装目录。比如：`/data/server/php/php72`。当我们有多个 `PHP` 版本的时候，可以通过目录名称来区别。

```
--prefix=/data/server/php/php72
```



#### 3.2）--with-config-file

设置 `PHP` 的配置目录。比如：`/data/server/php/php72/etc`。同样，可以通过目录名称来区分多个版本。

```
--with-config-file=/data/server/php/php72/etc
```



#### 3.3）--enable-fpm

开户 FPM 支持。在 `PHP 5.3.3` 版本开始内置了 `PHP-FPM(FastCGI Process Manager SAPI)`  模块。在此之前如果没有内置的话，必须通过打补丁的形式安装 `PHP-FPM`。所以，我们现在可以编译的时候开启。

```
# 开启
--enable-fpm

# 关闭
--disable-fpm
```



#### 3.4）--with-mysqli

设置 `PHP` 函数中 `mysqli_` 函数族由哪个库来提供支持。在还没有 `mysqlnd` 的时候，使用 `MySQL` 提供的一个库 `libmysql`来实现。由于 `PHP` 官方担心 `MySQL` 被 `Oracle` 收购之后开源协议的变动会给 `PHP` 社区发展带来一定的风险。于是 `PHP` 官方自己开发了一个兼容 `libmysql` 的 `MySQL` 数据库驱动。

`mysqlnd(MySQL Native Driver)`  在 `PHP 5.3.0` 版本引入。所以，后续的版本中全部使用 `mysqlnd` 来提供驱动即可。

```
--with-mysqli=mysqlnd
```



#### 3.5）--with-pdo-mysql

这个就是 `PDO` 使用 `MySQL` 的时候，用哪个 `mysql` 驱动来实现。与 `--with-mysqli` 一样。统一使用 `mysqlnd` 即可。

```
--with-pdo-mysql=mysqlnd
```



#### 3.6）--enable-static

编译安装的时候生成静态库。与之相对应的是动态库。静态库的优势就是加载的时候慢点，但是后续执行的时候就不需要再加载。稍微消耗内存一点。在服务器内存日益强大的今天，这点开销可以忽略不计。**用空间换时间**。

```
# 开启
--enable-static

# 关闭
--disable-static
```



#### 3.7）–-enable-ftp

开户 `FTP` 支持。`FTP` 的扩展已经内置了。只需要在编译安装的时候加 `--enable-ftp` 就能激活。需要现在 `FTP` 用得不多了。但是，也不保不齐哪天有这样的需求需要在代码中通过 `FTP` 协议来操作。比如，我曾经在对接一个银行项目的时候，对账的文件就是通过 `FTP` 来下载的。

```
# 开启
--enable-ftp

# 关闭
--disable-ftp
```



#### 3.8）--enable-inline-optimization

编译优化。

```
--enable-inline-optimization
```



#### 3.9）--enable-sockets

开启 `Socket` 扩展支持。`Socket` 扩展是基于流行的 `BSD sockets`，实现了和 `socket` 通讯功能的底层接口，它可以和客户端一样当做一个 `socket` 服务器。在 `PHP 5.0.0` 开始加入了对 `IPv6` 的支持。

这个扩展应用还是比较广泛的。目前也有其它替代的扩展。

```
# 开启
--enable-sockets

# 关闭
--disable-sockets
```



#### 3.10）--enable-pcntl

开启 `pcntl` 扩展。该扩展用于 `PHP` 控制进程所用。它默认是关闭的。所以，我们一定要通过此参数打开它。我曾经写过的一个库来生成多进程发送短信和 `APP PUSH`。

> 注：在 windows 系统是不支持这个扩展的。

```
# 开启
--enable-pcntl

# 关闭
--disable-pcntl
```



#### 3.11）--enable-posix

开启 `posix` 扩展支持。该扩展在 `PHP` 安全模式运行时冲突。当启用安全模式时应该强烈禁用此扩展。该扩展实现了 `IEEE 1003.1 (POSIX.1)` 文档中定义的规范。它通常与 `pcntl` 一起配合来开发多进程应用。比如它其中有一个功能是获取进程ID/父进程ID，以及检测进程是否存活。非常之有用。

比如知名的 `workerman` 框架就必须使用到 `pcntl` 与 `posix` 这两个扩展。

```
# 开启
--enable-posix

# 关闭
--disable-posix
```



#### 3.12）--with-gettext

设置 `gettext` 支持。`gettext` 函数实现了 `NLS (Native Language Support) API`，他可以用来国际化您的`PHP` 程序。要使用该模块，必须保证您的系统当中已经安装了 gettext 库。默认情况下会去  `/usr/local` 目录下去查找。

```
# 默认去 /usr/local 目录查找类库
--with-gettext
# 指定目录。这个目录是自己编译安装的目录。0.20.0
--with-gettext=/usr/local/gettext0.20.0
```

`CentOS` 安装 `gettext` 也挺简单的：

```
$ yum isntall -y gettext
```



#### 3.13）--enable-zip

开启 `zip` 扩展支持。这个很容易理解。特别是使用 `composer` 的时候，会用到这个扩展。

```
# 开启
--enable-zip
# 关闭
--disable-zip
```



#### 3.14）--enable-calendar

开启 `calendar` 扩展支持。该扩展提供的函数：https://www.php.net/manual/zh/book.calendar.php

```
# 开启
--enable-calendar
# 关闭
--disable-calendar
```



#### 3.15）--enable-bcmath

开启 `bcmath` 扩展支持。这个扩展提供了一些非常有用的任意大小与精度数学操作函数。在一些金融项目之中，就会使用到这些函数。强烈建议开启。

```
# 开启
--enable-bcmath
# 关闭
--disable-bcmath
```



#### 3.16）--enable-soap

开启 `soap` 扩展支持。`SOAP` 扩展用来编写 `SOAP 服务端和客户端，它支持 [SOAP 1.1](https://www.w3.org/TR/soap11/)、[SOAP 1.2](https://www.w3.org/TR/soap12/)、[WSDL 1.1](https://www.w3.org/TR/wsdl/) 规范的子集。

虽然目前用得少，但保不准哪天有的历史项目需要用到可能就会用得到。

```
# 开启
--enable-soap
# 关闭
--disable-soap
```



#### 3.17）--with-zlib

`zlib` 扩展可以对 `gzip（.gz）` 压缩包进行操作。上面我们已经安装了 `zlib` 类库。并且安装在了 `/usr/local` 目录下。默认不指定目录的情况下，`PHP` 也会在编译的时候也会去 `/usr/local` 目录加载。

```
# 默认
--with-zlib
# 指定目录。假如我们编译安装的 zlib 目录为：/usr/local/zlib1.2.11
--with-zlib=/usr/local/zlib1.2.11
```



#### 3.18）--with-iconv

开启 `iconv` 模块支持。此模块包含了 `iconv` 字符集转换功能的接口。该模块必须开启，因为这个模块提供的功能方法太有用了。https://www.php.net/manual/zh/book.iconv.php

```
# 设置
--with-iconv=/usr/local
```

因为在依赖库安装的小节，我们已经安装了 `libiconv` 扩展到 `/usr/local`。所以，我们这里可以直接使用。



#### 3.19）--with-gd

开启 `gd` 库。这个没得说。`GD` 库提供了图片处理的功能函数。比如，我们常见的验证码生成、图片裁剪、水印等。在依赖扩展安装环节我们并没有安装 `GD` 库。默认情况下`Linux` 系统已经安装了这个库。如果没有安装，可以使用 `yum` 命令安装：

```
--with-gd
```



#### 3.20）--enable-libxml









