# 0x02.6 PHP 7.3 版本特性

关于 PHP 7.3 版本的更新日志，可以查看官方文档：https://www.php.net/manual/zh/migration74.php



### 1）新增函数

```
array_key_first()
array_key_last()
gc_status()
hrtime()
is_countable()
net_get_interfaces()
fpm_get_status()
openssl_pkey_derive()
socket_wsaprotocol_info_export()
socket_wsaprotocol_info_import()
socket_wsaprotocol_info_release()
```

> 更多新增函数：https://www.php.net/manual/zh/migration73.new-functions.php



### 2）大小写不敏感的常量声明现已被废弃

大小写不敏感的常量声明现已被废弃。将 **`TRUE`** 作为第三个参数传递给 [define()](https://www.php.net/manual/zh/function.define.php) 将会导致一个废弃警告。大小写不敏感的使用（在读取时使用一个与声明时不同的大小写方式）也已被废弃。



### 3）命名空间中的 `assert()`

废弃：在一个命名空间中声明一个名为 *assert()* 的函数。 [assert()](https://www.php.net/manual/zh/function.assert.php) 函数属于引擎特殊处理的情况，当在命名空间中使用相同名字去定义 函数时也许会导致不一致的行为。



### 4）在字符串中搜索非字符串内容

废弃：将一个非字符串内容传递给字符串搜索函数。 在将来所有待搜索的内容都将被视为字符串，而不是 `ASCII` 编码值。如果需要依赖这个特性，你应该 要么显示地进行类型转换（转为字符串），或者显示地调用 [chr()](https://www.php.net/manual/zh/function.chr.php)。 以下是受到影响的方法：

```
strpos()
strrpos()
stripos()
strripos()
strstr()
strchr()
strrchr()
stristr()
```



### 5）`image2wbmp()` 已被废弃。



### 6）以下多字节字符串方法废弃

以下在文档中不存在的 *mbereg_\*()* 别名已被废弃。请使用相应的 *mb_ereg_\*()* 变体替代。

- **mbregex_encoding()**
- **mbereg()**
- **mberegi()**
- **mbereg_replace()**
- **mberegi_replace()**
- **mbsplit()**
- **mbereg_match()**
- **mbereg_search()**
- **mbereg_search_pos()**
- **mbereg_search_regs()**
- **mbereg_search_init()**
- **mbereg_search_getregs()**
- **mbereg_search_getpos()**
- **mbereg_search_setpos()**

