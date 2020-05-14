# 0x03.5 安全配置

关于 `PHP` 安全的配置，对于优化 `PHP` 项目来说是非常之重要的。除了保证系统配套软件（`Linux`、 `Nginx`、`PHP`、`Redis`、`MySQL` 以及系统底层类库）更新之外，还要对 `PHP` 配置进行相应的设置。



### 1）关闭错误显示

正式环境必须关闭 `PHP` 错误的显示。否则，被恶意用户（黑客）通过错误信息找到可攻击的漏洞，会造成不可挽回的损失。

```ini
display_errors = Off
display_startup_errors = Off
log_errors = On
error_logs = /data/logs/php/php72/php_error.log
```



### 2）关闭 `expose_php`

`expose_php` 决定是否暴露 `PHP ` 被显示在服务器上（例如在 `Web` 服务器的信息头中加上其签名：`X-Powered-By: PHP/5.3.7`)。

有经验的黑客会研究每个 `PHP` 版本的漏洞去寻找对应的攻击方法。暴露出来的信息越多，越危险。并且，该信息的显示对实际的开发和用户业务并无助力作用。

强烈建议关闭。



3）