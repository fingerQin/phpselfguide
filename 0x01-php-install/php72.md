# 0x01 PHP7.2 安装

下载&&安装：

```
$ wget https://www.php.net/distributions/php-7.2.30.tar.gz
$ tar zxvf php-7.2.30.tar.gz
$ cd php-7.2.30
$ ./configure --prefix=/data/server/php72 \
--enable-opcache \
--with-config-file-path=/data/server/php72/etc \
--with-mysqli=mysqlnd \
--with-pdo-mysql=mysqlnd \
--enable-fpm \
--enable-static \
--enable-inline-optimization \
--enable-sockets \
--enable-pcntl \
--with-gettext \
--enable-wddx \
--enable-zip \
--enable-calendar \
--enable-bcmath \
--enable-soap \
--with-zlib \
--with-iconv=/usr/local \
--with-gd \
--with-xmlrpc \
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

关于上面一系列参数的解释：

```
--prefix 指定 PHP 安装目录
--with-config-file-path 指定 PHP 配置文件目录
--with-freetype-dir 指定 freetype 库目录
--with-jpeg-dir 指定 libjpeg 库目录
--with-png-dir 指定 libpng 库目录
--with-zlib-dir 指定 zlib 库目录
--with-libxml-dir 指定 libxml 库目录
--with-curl 打开 CURL 支持
--enable-fpm 开户 fpm 支持。PHP 5.3.3 之后可用,不需要单独安装 fpm。
--enable-mbstring 多字节，字符串的支持
--with-gd 打开 GD 库支持
--with-openssl 打开 openssl 支持
--enable-pcntl 打开 pcntl 扩展支持。用于 PHP 多进程。
--enable-sockets 打开 sockets 支持
--with-xmlrpc 打开 xml-rpc 的 c 语言
--enable-zip 打开 zip 的支持
--enable-ftp 打开 ftp 的支持 
--with-xsl 打开 XSLT 文件支持。必须系统安装了 libxslt 扩展。
--disable-ipv6 关闭支持 ipv6。
--disable-debug 关闭 PHP Debug。正式环境关闭此项可提升性能。

```







