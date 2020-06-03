## 0x08.6 配置

本章会讲解 `MySQL` 的 `my.cnf` 配置文件。以及每个配置的含义。由于不同版本会新增或删除一些配置。所以，会出现部分配置不适用的情况。



### 1）配置节说明

```
[client] 代表客户端默认设置内容；

[mysql] 代表我们使用mysql命令登录mysql数据库时的默认设置；

[mysqld] 代表数据库自身的默认设置；
```



### 2）常规配置介绍

在 `8.2 ` 小节，我们知道了通过 `show variables` 查看 `mysql` 所有的配置。那么，现在我们来讲解一些常用且实用的配置。



#### 2.1）max_connections

设置客户端允许连接的最大连接数。如果客户端连接 `MySQL` 服务器的数量超过此值则会报：`Too many connections`。我们可以通过如下查看服务器最大允许连接的数量。此值不宜设置过大。越大消耗的系统内存越多。必须根据服务器的配置进行不断的微调。

该配置在 `[mysqld]` 节中设置。

根据网上有经验的人给出的值的计算公式为：max_used_connections / max_connections * 100% = 99.6% （理想值 ≈ 85%）。

查看服务器配置：

```sql
show variables like "max_connections";
```



#### 2.2）back_log

`MySQL` 能够暂存的连接数量。当 `MySQL` 线程在一个很短时间内得到非常多的连接请求，它就会起作用。如果 `MySQL` 的连接数据达到 `max_connections` 时，新的请求将会被存在堆栈中，以等待某一连接释放资源，该堆栈数量即 `back_log`，如果等待连接的数量超过 `back_log`，将不被接受连接资源。

> back_log值不能超过TCP/IP连接的侦听队列的大小。若超过则无效，查看当前系统的TCP/IP连接的侦听队列的大小命令：`cat /proc/sys/net/ipv4/tcp_max_syn_backlog`，目前系统为1024。对于 `Linux` 系统推荐设置为大于 512 的整数。

查看服务器配置:

```sql
show variables like "back_log";
```



#### 2.3）wait_timeout

指的是 `MySQL` 再关闭一个非交互的连接之前所需要等待的秒数。

对性能的影响：

- （1）如果设置太小，那么连接关闭的很快，从而使一些持久的连接不起作用

- （2）如果设置太大容易造成连接打开时间过长，在 `show processlist` 时，能够看到太多的 `sleep` 状态的连接，从而造成 `too many connections` 错误。

- （3）一般希望wait_timeuot尽可能的低。

查看服务器配置：

```sql
show variables like "wait_timeout";
```



#### 2.4）interative_timeout

指的是关闭一个交互的连接之前所需要等待的秒数。当我们使用命令行登录 `MySQL` 服务器的时候，此时该值生效。

查看服务器配置：

```sql
show variables like "interative_timeout";
```



#### 2.5）key_buffer_size

`key_buffer_size` 指定索引缓冲区的大小。它决定索引的处理速度，尤其是索引读的速度。通过检查状态值 `key_read_requests` 和 `key_reads`。可以知道`key_buffer_size` 设置是否合理。比例 `key_reads`/`key_read_requests` 应该尽可能的低。至少是 1:100，1:1000 更好（上述状态值可以使用 `show status like 'key_read%'`获得）。



未命中缓存的概率：

  `key_cache_miss_rate` = `key_reads`/`key_read_requests` * 100%

  `key_buffer_size` 只对 `MAISAM` 表起作用。

 

  如何调整 `key_buffer_size` 的值。

  默认的配置数时 8388608（8M），主机有 4G 内存可以调优值为 268435456（256M）。



#### 2.6）query_cache_size

查询缓存简称：`GC`。

使用查询缓存，`MySQL` 将查询结果存放在缓冲区中，今后对同样的 `select` 语句（区分大小写），将直接从缓冲区中读取结果。

一个 `SQL` 查询如果以 `select` 开头，那么 `MySQL` 服务器将尝试对其使用查询缓存。

注：两个 `SQL` 语句，只要相差哪怕是一个字符（例如 大小写不一样：多一个空格等），那么两个 `SQL` 将使用不同的 `cache`。

通过 `show ststus like ‘Qcache%'` 可以知道 `query_cache_size` 的设置是否合理。

 

`Qcache_free_blocks`：缓存中相邻内存块的个数。如果该值显示过大，则说明 `Query Cache` 中的内存碎片较多了。

注：当一个表被更新后，和他相关的 `cache block` 将被 `free`。但是这个 `block` 依然可能存在队列中，除非是在队列的尾部。可以用 `flush query cache` 语句来清空 `free blocks`。

`Qcache_free_memory:Query Cache` 中目前剩余的内存大小。通过这个参数我们可以较为准确的观察当前系统中的 `Query Cache` 内存大小是否足够，是需要增多还是过多了。

`Qcache_hits`：表示有多少次命中缓存。我们主要可以通过该值来验证我们的查询能缓存的效果。数字越大缓存效果越理想。

`Qcache_inserts`：表示多少次未命中而插入，意思是新来的 `SQL` 请求在缓存中未找到，不得不执行查询处理，执行查询处理后把结果 `insert` 带查询缓存中。这样的情况次数越多，表示查询缓存 应用到的比较少，效果也就不理想。

`Qcache_lowmen_prunes`：多少条 `Query` 因为内存不足而被清除出 `Query Cache`，通过 `Qcache_lowmem_prunes` 和 `Qcache_free_memory` 相互结合，能够更清楚的了解到我们系统中 `Query Cache` 的内存大小是否真的足够，是否非常频繁的出现因为内存不足而有 `Query` 被换出。这个数字最好是长时间来看，如果这个数字在不断增长，就表示可能碎片化非常严重，或者内存很少。

 

 `Qcache_queries_in_cache`：当前 `Query Cache` 中 `cache` 的 `Query` 数量。

 `Qcache_total_blocks`：当前 `Query Cache` 中 `block` 的数量。

 

查询服务器关于 `query_cache` 的配置。

各字段的解释：

`query_cache_limit`：超出此大小的查询将不被缓存。

`query_cache_min_res_unit`：缓存块的最小大小，`query_cache_min_res_unit` 的配置是一柄双刃剑，默认是 4KB ，设置值大对大数据查询有好处，但是如果你查询的都是小数据查询，就容易造成内存碎片和浪费。

`query_cache_size`：查询缓存大小（注：`QC` 存储的单位最小是 1024byte，所以如果你设定的一个不是1024 的倍数的值。这个值会被四舍五入到最接近当前值的等于1024 的倍数的值。）

`query_cache_type`：缓存类型，决定缓存什么样子的查询，注意这个值不能随便设置必须设置为数字，可选值以及说明如下：

- 0：`OFF` 相当于禁用了

- 1：`ON` 将缓存所有结果，除非你的select语句使用了 `SQL_NO_CACHE` 禁用了查询缓存

- 2：`DENAND` 则只缓存select语句中通过 `SQL_CACHE` 指定需要缓存的查询。

`query_cache_wlock_invalidate`：当有其他客户端正在对 `MyISAM` 表进行写操作时，如果查询在 `query cache` 中，是否返回 `cache` 结果还是等写操作完成在读表获取结果。

 

查询缓存碎片率：`Qcache_free_block`/`Qcache_total_block` * 100%

如果查询缓存碎片率超过 20%，可以用 `flush query cache` 整理缓存碎片，或者试试减小 `query_cache_min_res_unit`，如果你的查询都是小数据量的话。

 

查询缓存利用率：（`query_cache_size` - `Qcache_free_memory`)/`query_cache_size` * 100%

查询缓存利用率在 25% 以下的话说明 `query_cache_size` 设置过大，可以适当减小：查询缓存利用率在 80% 以上而且 `Qcache_lowmem_prunes` > 50

的话说明 `query_cache_size` 可能有点小，要不就是碎片太多。

查询缓存命中率：`Qcache_hits`/(`Qcache_hits` + `Qcache_inserts`) * 100%

 

`Query Cache` 的限制

- a）所有子查询中的外部查询 `SQL` 不能被 `Cache`：

- b）在 `procedure`，`function` 以及 `trigger` 中的 `Query` 不能被 `Cache`

- c）包含其他很多每次执行可能得到不一样的结果的函数的 `Query` 不能被 `Cache`



#### 2.7）max_connect_errors

是一个 `MySQL` 中与安全有关的计数器值，它负责阻止过多尝试失败的客户端以防止暴力破解密码的情况。当超过指定次数，`MySQL` 服务器将禁止 `host` 的连接请求。直到 `mysql` 服务器重启或通过 `flush hosts` 命令清空此 `host` 的相关信息。（与性能并无太大的关系）。

说到这个值有可能会出现在我们定期更换账号。但是程序未及时修改的情况下。导致大量的连接错误而禁止连接。



#### 2.8）sort_buffer_size

每个需要排序的线程分配该大小的一个缓冲区。增加这值加速 `ORDER BY` 或 `GROUP BY` 操作 。

`sort_buffer_size` 是一个 `connection` 级的参数。在每个 `connection`（`session`）第一次需要使用这个 `buffer` 的时候，一次性分配设置的内存。

`sort_buffer_size`：并不是越大越好，由于是 `connection` 级的参数，过大的设置 + 高并发可能会耗尽系统的内存资源。例如：500 个连接将会消耗500 * sort_buffer_size(2M) = 1G。



#### 2.9）max_allowed_packet

根据配置文件限制 `server` 接受的数据包大小。

- 客户端发送到mysql 服务端的单个 `SQL STATEMENT`。
- 服务端发送到客户端的单行数据。
- `master` 发往 `slave` 的一个 `binary log event`。

> MySQL 8.0单个packet可以允许的最大值是1GB。



#### 2.10）join_buffer_size

用于表示关联缓存的大小，和 `sort_buffer_size` 一样，该参数对应的分配内存也是每个连接独享。



#### 2.11）thread_cache_size

服务器线程缓存。

这个值表示可以重新利用保存在缓存中的线程数量。当断开连接时，那么客户端的线程将被放到缓存中以响应下一个客户而不是销毁（前提时缓存数未达上限）。如果线程重新被请求，那么请求将从缓存中读取，如果缓存中是空的或者是新的请求，这个线程将被重新请求，那么这个线程将被重新创建，如果有很多新的线程。增加这个值可以改善系统性能，通过比较 `Connections` 和 `Threads_created` 状态的变量，可以看到这个变量的作用。

设置规则如下：1G 内存配置为 8，2G 内存为 16。服务器处理此客户的线程将会缓存起来以响应下一个客户而不是被销毁（前提是缓存数未到达上限）。

 

- `Threads_cached`：代表当前此时此刻线程缓存中有多少空闲线程。

- `Threads_connected`：代表当前已建立连接的数量，因为一个连接就需要一个线程，所以也可以看成当前被使用的线程数。

- `Threads_created`：代表最近一次服务启动，已创建线程的数量，如果发现 `Threads_created` 值过大的话，说明 `MySQL` 服务器一直在创建线程，这也比较消耗资源，可以适当增加配置文件中 `thread_cache_size` 值。

- `Threads_running`：代表当前激活的（非睡眠状态）线程数。并不是代表正在使用的线程数，有时候连接已建立，但是连接处于 `sleep` 状态。



#### 2.12）innodb_buffer_pool_size

对于 `innodb` 存储引擎的表来说，`innodb_buffer_pool_size` 的作用相当于 `key_buffer_size` 对于 `MyISAM` 表的作用一样。`Innodb` 使用该参数指定大小的内存来缓冲数据和索引。最大可以把该值设置成物理内存的 80%。



#### 2.13）innodb_flush_log_at_trx_commit

主要控制了 `innodb` 将 `log buffer` 中的数据写入日志文件并 `flush` 磁盘的时间点，取值分别为0，1，2。

实际测试发现，该值对插入数据的速度影响非常大，设置为 2 时插入10000 条记录只需要两秒。设置为 0 时只需要一秒，设置为 1 时，则需要 229 秒。因此，`MySQL` 手册也建议尽量将插入操作合并成一个事务，这样可以大幅度提高速度。



#### 2.14）innodb_thread_concurrency

 此参数用来设置 `innodb` 线程的并发数，默认值为 0 表示不被限制。若要设置则与服务器的 CPU 核心数相同或是 CPU 的核心数的 2 倍。



#### 2.15）innodb_log_buffer_size

此参数确定日志文件所用的内存大小，以 M 为单位。缓冲区更大能提高性能，对于较大的事务，可以增大缓存大小。



#### 2.16）innodb_log_file_size

此参数确定数据日志文件的大小，以 M 为单位，更大的设置可以提高性能。



#### 2.17）innodb_log_files_in_group

为提高性能，`MySQL` 可以以循环方式将日志文件写到多个文件。推荐设置为 3。



#### 2.18）read_buffer_size

`MySQL` 读入缓冲区大小。对表进行顺序扫描的请求将分配到一个读入缓冲区 `MySQL` 会为他分配一段内存缓冲区。



#### 2.19）read_rnd_buffer_size

`MySQL` 的随机读（查询操作）缓冲区大小。当按任意顺序读取行时（例如，按照排序顺序），将分配到一个随机都缓冲区。进行排序查询时，`MySQL` 会首先扫描一遍该缓冲区，以避免磁盘搜索，提高查询速度，如果需要排序大量数据，可适当调高该值。但是 `MySQL` 会为每个客户连接发放该缓冲空间，所以应尽量适当设置该值，以避免内存消耗过大。

> 注：顺序读是根据索引的叶节点数据就能顺序的读取所需要的行数据。随机读是指一般需要根据辅助索引叶节点中的主键寻找侍其巷进行数据，而辅助索引和主键所在的数据端不同，因此访问方式是随机的。



#### 2.20）bulk_insert_buffer_size

批量插入数据缓存大小，可以有效的提高插入效率，默认为8M。



#### 2.21）binary log

`binlog_cache_size`=2M  // 为每个 `session` 分配的内存，在事务过程中用来存储二进制日志的缓存，提高记录 `bin-log` 的效率。

`max_binlog_cache_size`=8M // 表示的是 `binlog` 能够使用的最大 `cache` 内存大小。

`max_binlog_size`=512M // 指定 `binlog` 日志文件的大小。不能将变量设置为大于 1G 或小于 4096 字节。默认值为1G。在导入大容量的 `sql` 文件时，建议关闭。`sql_log_bin`，否则硬盘扛不住，而且建议定期做删除。

`expire_logs_days`=7 // 定义了 `mysql` 清除过期日志的时间。



#### 2.22）max_heap_table_size

独立的内存表所允许的最大容量。此选项为了防止意外创建一个超大的内存表导致永尽所有的内存资源。



#### 2.23）default_table_type

如果在创建表示没有特别执行表类型,将会使用此值。

示例：

```ini
default_table_type = InnoDB
```



#### 2.24）log-bin

打开二进制日志功能。

在复制(`replication`)配置中,作为 `MASTER` 主服务器必须打开此项。

如果你需要从你最后的备份中做基于时间点的恢复,你也同样需要二进制日志。

示例：

```ini
log-bin = mysql-bin
```



#### 2.25）log_slow_queries

记录慢速查询。

慢速查询是指消耗了比 `long_query_time` 定义的更多时间的查询。

如果 `log_long_format` 被打开，那些没有使用索引的查询也会被记录。

如果你经常增加新查询到已有的系统内的话。一般来说这是一个好主意。

示例：

```ini
log_slow_queries = on
```



#### 2.26）long_query_time

所有的使用了比这个时间(以秒为单位)更多的查询会被认为是慢速查询。

不要在这里使用 1。 否则会导致所有的查询,甚至非常快的查询页被记录下来(由于 `MySQL` 目前时间的精确度只能达到秒的级别)。

示例：

```ini
long_query_time = 3
```



#### 2.27）log_long_format

打开此项会记录使得那些没有使用索引的查询也被作为到慢速查询附加到慢速日志里。

示例：

```ini
log_long_format = on
```



#### 2.28）slow_query_log_file

设置慢查询记录的文件路径。

示例：

```ini
slow_query_log_file = /data/mysql/slow.log
```



#### 2.29）min_examined_row_limit

指定由同一语句造成慢查询次数超过此值的慢查询。

也就是说，产生慢查询之后，还要统计累计次数是否大于此值。大于才记录。

示例：

```ini
min_examined_row_limit = 100
```



#### 2.30）log_slow_slave_statements

是否记录由从库产生的慢查询。

示例：

```ini
log_slow_slave_statements = off
```



#### 2.31）no-auto-rehash

是否开户命令行自动补全功能。

示例：

```ini
no-auto-rehash
```







