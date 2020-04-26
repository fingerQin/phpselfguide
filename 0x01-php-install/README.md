# 0x01 PHP 安装

> PHP 软件的安装是每一位 PHP 开发者必须掌握的一个技能。很多人可能在 windows 系统下安装 PHP 软件。觉得安装 PHP 是非常容易的一件事情。然后在 Linux 环境下安装 PHP 却并不容易。所以，我们此系列教程如果没有特别说明，全部在 CentOS7 for linux 环境下操作。

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
PHP 很多的内置扩展或开发中常用的扩展会依赖系统底层库的支持。所以，要想使用这些功能，需要提前安装这些库。



