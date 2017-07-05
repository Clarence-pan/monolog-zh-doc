# 使用 Monolog

- [安装](#安装)
- [核心概念](#核心概念)
- [日志级别](#日志级别)
- [配置一个日志服务](#配置一个日志服务)
- [为记录添加额外的数据](#为记录添加额外的数据)
- [使用通道](#使用通道)
- [自定义日志格式](#自定义日志格式)

## 安装

Monolog 在 Packagist ([monolog/monolog](http://packagist.org/packages/monolog/monolog)) 上可用，并且可以通过 [Composer](http://getcomposer.org/) 安装:

```bash
composer require monolog/monolog
```

如果你不使用 Composer, 那你可以从 GitHub 上获取代码，然后使用任何兼容 PSR-0 的自动加载器（比如[Symfony2 ClassLoader 组件](https://github.com/symfony/ClassLoader)）来加载 Monolog 的类

## 核心概念

每一个日志服务实例 (`Logger`) 都有一个通道（名称），并有一个处理器 (Handler)栈. 无论何时你添加一条 [记录](message-structure.md) 到对应的日志服务实例，这个处理器栈将被遍历一遍：每个处理器都将依次决定是否要处理这条记录，而如果要处理，则遍历结束（译注：类似DOM事件冒泡）。

这样子可以创建非常灵活的日志配置。比如一个 `StreamHandler` 可以把所有日志都写入磁盘，而上面加个`MailHandler` 可以把错误日志作为邮件发送出去。处理器还有一个 `$bubble` 属性定义了是否屏蔽某条记录或者处理了某条记录。在这个示例中，配置 `MailHandler` 的 `$bubble` 参数为 `false` 则意味着 `MailHandler` 将不会把自己已处理过的记录继续冒泡给 `StreamHandler`.

你可以创建许多日志服务实例(`Logger`)，每一个则定义一个通道（比如数据库、请求、路由...）。而每一个日志服务实例都可以组合各种各样的处理器，可以共享处理器也可以不共享。这个通道将会在日志中反映出来，从而允许你可以很容易地查看或者筛选记录。

每一个处理还会有一个格式化器（Formatter）。如果你没有配置一个，则一个有意义的默认的格式化器将被创建。格式化器用来规范化并格式化输入的记录，以便处理器能输出一些有用的信息。

不支持自定义的严重性级别。只支持使用[RFC 5424](http://tools.ietf.org/html/rfc5424)中定义的八个级别（调试/Debug、信息/Info、提示/Notice、警告/Warning、错误/Error、严重/Critical、警报/Alert、紧急/Emergency）来作为基本的筛选目的。不过，如果为了排序或者其他需要灵活性的使用场景，你可以添加加工程序（Processor）从而可以在（处理器）处理前添加额外的信息（标签、用户IP...）。

## 日志级别

Monolog 支持一下 [RFC 5424](http://tools.ietf.org/html/rfc5424) 中的日志级别：

- **调试/DEBUG** (100): 详细的调试信息。

- **信息/INFO** (200): 有意义的事件，比如用户登录、SQL日志。

- **提示/NOTICE** (250): 正常但是值得注意的事件。

- **警告/WARNING** (300): 异常事件，但是并不是错误。比如使用了废弃了的API，错误地使用了一个API，以及其他不希望发生但是并非必要的错误。

- **错误/ERROR** (400): 运行时的错误，不需要立即注意到，但是需要被专门记录并监控到。

- **严重/CRITICAL** (500): 边界条件/危笃场景。比如应用组件不可用了，未预料到的异常。

- **警报/ALERT** (550): 必须立即采取行动。比如整个网站都挂了，数据库不可用了等。这种情况应该发送短信警报，并把你叫醒。

- **紧急/EMERGENCY** (600): 紧急请求：系统不可用了。

## 配置一个日志服务

这里有一个基本的配置，可以记录日志到文件，并在 `DEBUG` 级别下记录到 firephp 中:

```php
<?php

use Monolog\Logger;
use Monolog\Handler\StreamHandler;
use Monolog\Handler\FirePHPHandler;

// 创建日志服务
$logger = new Logger('my_logger');

// 添加一些处理器
$logger->pushHandler(new StreamHandler(__DIR__.'/my_app.log', Logger::DEBUG));
$logger->pushHandler(new FirePHPHandler());

// 现在你就可以用日志服务了
$logger->info('My logger is now ready');
```

解释一下。第一步是创建日志服务实例，这个实例后将在代码中用到。唯一的参数是通道的名称，它在你有多个日志服务实例的时候很有用。（更多详情参见下面）

这个日志服务实例自己是不是知道如何处理一条日志记录的。它把记录代理给了一些处理器。上面的代码中注册了两个处理器到栈中，以便允许使用两种不同的方式来处理日志记录。

注意，`FirePHPHandler` 是被先调用的，因而它被添加到了栈顶。这允许你临时添加一个禁止冒泡的处理器从而允许你覆盖其他配置的日志（处理器）。

> 如果你单独使用 Monolog, 并且在寻找一种简单的方式来配置许多处理器，那可以用
> [theorchard/monolog-cascade](https://github.com/theorchard/monolog-cascade) 。
> 它可以帮你使用PHP数组、YAML或者JSON来构建复杂的日志配置。

## 为记录添加额外的数据

Monolog 提供了两种不同的方式来为简单的文本消息增加额外的信息

### 使用上下文(`context`)

第一种方式是使用上下文（`context`），这允许你在传递记录的时候传递一个数组格式的数据：

```php
<?php

$logger->info('Adding a new user', array('username' => 'Seldaek'));
```

简单的处理器（比如`StreamHandler`）将只是把数组转换成字符串。而复杂的处理器则可以利用上下文的优点（如 FirePHP 则将以一种优美的方式显示数组）。

### 使用加工程序(`Processor`)

第二种方式是使用加工程序来为所有的记录添加额外数据。加工程序可以是任何可以调用的函数。
加工程序接收日志记录作为参数，并且需要在修改了`extra`字段后再返回日志记录。让我们来写一个添加一些假数据的加工程序：

```php
<?php

$logger->pushProcessor(function ($record) {
    $record['extra']['dummy'] = 'Hello world!';

    return $record;
});
```

Monolog提供了一些内置的加工程序，你可以在你的项目中使用它们。请参见[对应的章节](./02-handlers-formatters-processors.md#加工程序).

> 小技巧：加工程序可以被注册到一个特定的处理器上而不是直接在日志服务实例上，从而可以只在对应的处理器上生效。

## 使用通道

通道是一种非常棒的方式来区分是应用的哪个部分的日志被记录下来的。这通常在大型项目中非常有用（而且被Symfony2的MonologBundle所使用）。

假设有两个日志服务实例共享了一个处理器，这个处理器将日志写入单个日志文件。通道则将允许你来区分是哪个日志服务实例记录了哪条日志。你可以很简单地通过通道来筛选日志。

```php
<?php

use Monolog\Logger;
use Monolog\Handler\StreamHandler;
use Monolog\Handler\FirePHPHandler;

// 创建一些处理器
$stream = new StreamHandler(__DIR__.'/my_app.log', Logger::DEBUG);
$firephp = new FirePHPHandler();

// 创建应用的主要日志服务实例
$logger = new Logger('my_logger');
$logger->pushHandler($stream);
$logger->pushHandler($firephp);

// 使用另外的通道来创建安全相关的日志服务示例
$securityLogger = new Logger('security');
$securityLogger->pushHandler($stream);
$securityLogger->pushHandler($firephp);

// 或者克隆第一个，只是改变下通道
$securityLogger = $logger->withName('security');
```

## 自定义日志格式

在Monolog中，可以很简单地来自定义日志的格式，无论是写入文件、套接字、邮件、数据库还是其他处理器。大多数处理器都是用 `$record['formatted']` 这个值来自动写入日志设备。这个值依赖格式化器的配置。你可以选择预定义的格式化器类，也可以自己写一个（比如一个可读的多行文本文件）。

想要配置一个预定义的格式化器类，可以直接把它设置为处理器的字段：

```php
// 默认的日期格式是 "Y-m-d H:i:s"
$dateFormat = "Y n j, g:i a";
// 默认的输出格式是 "[%datetime%] %channel%.%level_name%: %message% %context% %extra%\n"
$output = "%datetime% > %level_name% > %message% %context% %extra%\n";
// 最后创建一个格式化器
$formatter = new LineFormatter($output, $dateFormat);

// 创建一个处理器
$stream = new StreamHandler(__DIR__.'/my_app.log', Logger::DEBUG);
$stream->setFormatter($formatter);

// 将其绑定到日志服务对象上
$securityLogger = new Logger('security');
$securityLogger->pushHandler($stream);
```

// 你还可以在多个处理器之间复用同一个格式化器，并且在多个日志服务实例间共享这些处理器。

[处理器、格式化器和加工程序](02-handlers-formatters-processors.md) &rarr;
