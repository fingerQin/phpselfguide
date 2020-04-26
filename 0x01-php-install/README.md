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



#### 2.7）其他方式安装库

如果你的系统是 CentOS 的话，完全不用上面那么麻烦。直接可以通过 `yum` 来安装。只不过，有些库在 `yum` 源中版本太低，导致我们不得不使用源码安装。如果安装过程中，出现版本太低的情况。大家选择手动编译安装。

否则，可以像下面这样安装其他类库：

```
$ sudo -s
$ LANG=C
$ yum install -y gcc gcc-c++ autoconf libxml2 libxml2-devel libjpeg libjpeg-devel libjpeg-turbo-devel libiconv-devel freetype freetype-devel libpng libpng-devel gd-devel libcurl-devel libxslt-devel zlib zlib-devel glibc glibc-devel glib2 glib2-devel curl curl-devel openssl openssl-devel
```

这里已经安装了上面全部的库。所以，使用 CentOS 系统，在安装这些库的时候非常之方便。如果不是 CentOS 系统的话，就要一个一个编译安装了。当然，其他的 Linux 系统也有自己的类库管理工具。



> 注：为了防止类库安装之后没有被系统加载。我们可以手动执行这个命令立即生效：`/sbin/ldconfig`









