套接字处理器
===============

这个处理器允许你把你的日志通过 [fsockopen](http://php.net/fsockopen)
或 [pfsockopen](http://php.net/pfsockopen) 写到套接字中。

主要是，当你需要提高性能而避免在不同请求之间关闭/打开连接的时候，持久化的套接字非常有用。

你可以使用 `unix://` 前缀来访问 Unix 套接字，而 `udp://` 前缀来打开 UDP 套接字，从而替代默认的 TCP 套接字。


基本示例
-------------

```php
<?php

use Monolog\Logger;
use Monolog\Handler\SocketHandler;

// 创建日志服务
$logger = new Logger('my_logger');

// 创建处理器
$handler = new SocketHandler('unix:///var/log/httpd_app_log.socket');
$handler->setPersistent(true);

// 现在添加处理器
$logger->pushHandler($handler, Logger::DEBUG);

// 现在你可以用这个日志服务了
$logger->info('My logger is now ready');

```

在这个示例中，使用 syslog-ng, 你就可以看到日志服务器上的这条日志了：

    cweb1 [2012-02-26 00:12:03] my_logger.INFO: My logger is now ready [] []

