# 0x03.7 上传配置

上传功能在 `Web` 系统中非常重要且常用。对它的配置，我们也需要重点掌握。

关于文件上传的拓展知识见官方文档：https://www.php.net/manual/zh/features.file-upload.php



配置示例：

```ini
file_uploads = On
upload_tmp_dir = ""
upload_max_filesize = 50M
max_file_uploads = 20
max_input_vars = 1000
max_input_nesting_level = 64
max_input_time = 60
```

以上就是关于文件上传的配置。

接下来我们对每一个配置进行说明。



### 1）`file_uploads`

从配置名称就可以知道，它是决定是否开启文件上传功能的配置项。默认值：`On`。



### 2）`upload_tmp_dir`

设置文件上传时保存的临时目录。默认值：`NULL`。



### 3）`upload_max_filesize`

设置上传文件允许的最大文件大小。单位：字节。默认值：`2M`。



### 4）`max_file_uploads`

允许同时上传的最大文件数。从 `PHP 5.3.4` 开始，上传字段提交时留空不计入此限制。



### 5）`max_input_vars`

提交字段最大数量。默认值：1000。



### 6）`max_input_nesting_level`

变量值递归数量。比如，我们表单中会存在 `checkbox` 复选框。或者我们通过 `CURL` 提交多维数组值的时候。这个参数就是来限制维度的。



### 7）`max_input_time`

脚本解析输入数据（类似 `POST` 和 `GET`）允许的最大时间，单位是秒。 它从接收所有数据到开始执行脚本进行测量的。

这个参数为了避免用户网络环境不好，处理低速数据传输的模式。缓解服务器压力，设置一个最大超时时间来避免。

> 待考证：据说 `max_input_time` 的时候不会计入 `max_execution_time` 的时间内。

