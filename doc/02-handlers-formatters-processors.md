# 处理器、格式化器和加工程序

- [处理器](#处理器)
  - [记录日志到文件和syslog](#记录日志到文件和syslogg)
  - [发送报警和邮件](#发送报警和邮件)
  - [特定日志服务与基于网络的日志](#特定日志服务与基于网络的日志)
  - [开发环境中的日志](#开发环境中的日志)
  - [记录日志到数据库](#记录日志到数据库)
  - [封装器/特殊处理器](#封装器--特殊处理器)
- [格式化器](#格式化器)
- [加工程序](#加工程序)
- [第三方库](#第三方库)

## 处理器

### 记录日志到文件和syslog

- [_StreamHandler_](../src/Monolog/Handler/StreamHandler.php): 记录日志到任何PHP流中，可以用这个来记录到文件中。
- [_RotatingFileHandler_](../src/Monolog/Handler/RotatingFileHandler.php): 记录到一个文件中，并且每天创建一个日志文件。
  这个还会删除老于 `$maxFiles` 的文件。如果要更高性能的配置，你应该使用 [logrotate](http://linuxcommand.org/man_pages/logrotate8.html) ；而这个只是一个简单粗暴的解决方案。
- [_SyslogHandler_](../src/Monolog/Handler/SyslogHandler.php): 记录日志到syslog.
- [_ErrorLogHandler_](../src/Monolog/Handler/ErrorLogHandler.php): 记录日志到PHP的 [`error_log()`](http://docs.php.net/manual/en/function.error-log.php) 函数。
- [_ProcessHandler_](../src/Monolog/Handler/ProcessHandler.php): 记录日志到任何进程的 [标准输入流（STDIN）](https://en.wikipedia.org/wiki/Standard_streams#Standard_input_.28stdin.29) , 需要指定一个命令（译注：这个命令指shell命令，以启动进程，然后写入该进程的标准输入流）。

### 发送报警和邮件

- [_NativeMailerHandler_](../src/Monolog/Handler/NativeMailerHandler.php): 使用PHP的[`mail()`](http://php.net/manual/en/function.mail.php) 函数发送邮件。
- [_SwiftMailerHandler_](../src/Monolog/Handler/SwiftMailerHandler.php): 使用一个 [`Swift_Mailer`](http://swiftmailer.org/) 的实例来发送邮件.
- [_PushoverHandler_](../src/Monolog/Handler/PushoverHandler.php): 通过 [Pushover](https://www.pushover.net/) API 来发送手机推送消息。
- [_HipChatHandler_](../src/Monolog/Handler/HipChatHandler.php): 使用 [HipChat](http://hipchat.com) 的 API 来发送到其内的一个聊天室中。
- [_FlowdockHandler_](../src/Monolog/Handler/FlowdockHandler.php): 记录日志到[Flowdock](https://www.flowdock.com/)的账户上。
- [_SlackbotHandler_](../src/Monolog/Handler/SlackbotHandler.php): 通过 Slackbot 接入钩子来记录日志到一个 [Slack](https://www.slack.com/) 账户上。
- [_SlackWebhookHandler_](../src/Monolog/Handler/SlackWebhookHandler.php): 通过 Slack 的 Webhook 来记录日志到一个 [Slack](https://www.slack.com/) 账户上。
- [_SlackHandler_](../src/Monolog/Handler/SlackHandler.php): 通过 Slack 的 API 来记录日志到一个 [Slack](https://www.slack.com/) 账户上。（这个配置比较复杂）
- [_SendGridHandler_](../src/Monolog/Handler/SendGridHandler.php): 通过 SendGrid API 来发送邮件。
- [_MandrillHandler_](../src/Monolog/Handler/MandrillHandler.php): 通过 Mandrill API，使用一个 [`Swift_Message`](http://swiftmailer.org/) 的实例来发送邮件。
- [_FleepHookHandler_](../src/Monolog/Handler/FleepHookHandler.php): 使用 Webhook 来记录日志到一个 [Fleep](https://fleep.io/) 的会话中。
- [_IFTTTHandler_](../src/Monolog/Handler/IFTTTHandler.php): 触发一个 [IFTTT](https://ifttt.com/maker) 触发器，携带有日志通道、级别名称和消息。

### 特定日志服务与基于网络的日志

- [_SocketHandler_](../src/Monolog/Handler/SocketHandler.php): 记录日志到 [套接字](http://php.net/fsockopen) 中, 可以用这个来记录到 UNIX 和 TCP 套接字。参见[示例](sockets.md).
- [_AmqpHandler_](../src/Monolog/Handler/AmqpHandler.php): 记录日志到一个兼容 [amqp](http://www.amqp.org/) 的服务器上。依赖 [php-amqp](http://pecl.php.net/package/amqp) 扩展 (1.0+).
- [_GelfHandler_](../src/Monolog/Handler/GelfHandler.php): 记录日志到一个 [Graylog2](http://www.graylog2.org) 服务器上。
- [_CubeHandler_](../src/Monolog/Handler/CubeHandler.php): 记录日志到一个[Cube](http://square.github.com/cube/) 服务器上。
- [_RavenHandler_](../src/Monolog/Handler/RavenHandler.php): 使用 [raven](https://packagist.org/packages/raven/raven) 记录日志到一个[Sentry](http://getsentry.com/) 服务器上。 
- [_ZendMonitorHandler_](../src/Monolog/Handler/ZendMonitorHandler.php): 记录日志到 Zend Server 上的 Zend Monitor 中。
- [_NewRelicHandler_](../src/Monolog/Handler/NewRelicHandler.php): 记录日志到一个[NewRelic](http://newrelic.com/) 应用中。
- [_LogglyHandler_](../src/Monolog/Handler/LogglyHandler.php): 记录日志到一个[Loggly](http://www.loggly.com/) 帐户中。
- [_RollbarHandler_](../src/Monolog/Handler/RollbarHandler.php): 记录日志到一个[Rollbar](https://rollbar.com/) 账户中。
- [_SyslogUdpHandler_](../src/Monolog/Handler/SyslogUdpHandler.php): 记录日志到一个远程的 [Syslogd](http://www.rsyslog.com/) 服务器上。
- [_LogEntriesHandler_](../src/Monolog/Handler/LogEntriesHandler.php): 记录日志到一个[LogEntries](http://logentries.com/) 账户中。
- [_LogmaticHandler_](../src/Monolog/Handler/LogmaticHandler.php): 记录日志到一个[Logmatic](http://logmatic.io/) 账户中。
- [_SqsHandler_](../src/Monolog/Handler/SqsHandler.php): 记录日志到一个 [AWS SQS](http://docs.aws.amazon.com/aws-sdk-php/v2/guide/service-sqs.html) 队列中。

### 开发环境中的日志

- [_FirePHPHandler_](../src/Monolog/Handler/FirePHPHandler.php): [FirePHP](http://www.firephp.org/) 的处理器，可以生成 [FireBug](http://getfirebug.com/) 内的 `console` 消息。
- [_ChromePHPHandler_](../src/Monolog/Handler/ChromePHPHandler.php): [ChromePHP](http://www.chromephp.com/) 的处理器, 可以生成 Chrome 内的 `console` 消息。
- [_BrowserConsoleHandler_](../src/Monolog/Handler/BrowserConsoleHandler.php): 发送日志到浏览器中的 Javascript `console` 的处理器，不需要浏览器扩展。大多数浏览器都支持 `console` API.
- [_PHPConsoleHandler_](../src/Monolog/Handler/PHPConsoleHandler.php): [PHP Console](https://chrome.google.com/webstore/detail/php-console/nfhmhhlpfleoednkpnnnkolmclajemef) 的处理器, 可以生成 Chrome 内的 `console` 消息和弹出的推送通知消息。

### 记录日志到数据库

- [_RedisHandler_](../src/Monolog/Handler/RedisHandler.php): 记录日志到一个[redis](http://redis.io) 服务器上。
- [_MongoDBHandler_](../src/Monolog/Handler/MongoDBHandler.php): 通过 [Mongo](http://pecl.php.net/package/mongo) 扩展来记录日志到一个 MongoDB 服务器上。
- [_CouchDBHandler_](../src/Monolog/Handler/CouchDBHandler.php): 记录日志到一个CouchDB 服务器上。
- [_DoctrineCouchDBHandler_](../src/Monolog/Handler/DoctrineCouchDBHandler.php): 通过 Doctrine CouchDB ODM 记录日志到一个CouchDB 服务器上。
- [_ElasticSearchHandler_](../src/Monolog/Handler/ElasticSearchHandler.php): 记录日志到一个 Elastic Search 服务器上。
- [_DynamoDbHandler_](../src/Monolog/Handler/DynamoDbHandler.php): 使用 [AWS SDK](https://github.com/aws/aws-sdk-php) 记录日志到一个 DynamoDB 表中。

### 封装器/特殊处理器

- [_FingersCrossedHandler_](../src/Monolog/Handler/FingersCrossedHandler.php):
  一个非常有意思的封装器。它接收一个日志服务实例作为参数，并且会把所有级别的日志都积累下来
  直到有一条记录超过了预定义的严重程度级别。那个时候，可以将发送所有的记录，包括那些低级别
  的，到它所封装的那个日志服务实例中。这意味着除非发生了一个错误，否则你将不会在日志中看到
  任何东西；然而当错误发生的时候，你则可以看到所有的信息，包括调试和信息基本的记录。这样，
  当且仅当你需要的时候，它提供给了你所有需要的信息。
- [_DeduplicationHandler_](../src/Monolog/Handler/DeduplicationHandler.php):
  如果当严重错误发生时你发送推送消息或者邮件的，这个会很有用。它接受一个日志服务实例作为
  参数，并会积累所有级别的日志记录直到请求结束（或者`flush()`被调用）。那时，它会将所有
  日志记录都发送到那个封装的日志服务实例中，额外的前提是这些记录在给定的时间段（默认60秒）
  内是唯一的。这个的主要用途是防止如数据库挂了的严重错误导致了一堆推送消息。添加这个处理
  器可以减少推送消息的数量到一个可控程度。

- [_WhatFailureGroupHandler_](../src/Monolog/Handler/WhatFailureGroupHandler.php):
   这个处理器继承了 _GroupHandler_ 并忽略了每个子处理器抛出的异常。这允许你忽略一些问
   题，如一个远程的TCP连接挂了但你不希望你的整个应用宕掉，而希望继续让其他处理器处理日
   志。
- [_BufferHandler_](../src/Monolog/Handler/BufferHandler.php): 
  这个处理器会缓存所有的日志记录，直到它接收到 `close()` 调用，然后它会调用一次它所封装的
  日志服务实例上的 `handleBatch()` 方法。这在这种情况下非常有用：一次请求想要只发送一封
  邮件，里面包含了所有的日志记录 -- 而非每条日志记录都发送一封邮件。
- [_GroupHandler_](../src/Monolog/Handler/GroupHandler.php): 
  这个处理器把其他处理器组合起来。每条接收到的记录都将发送给所有配置在其内的处理器中。
- [_FilterHandler_](../src/Monolog/Handler/FilterHandler.php): 
  这个处理器只允许给定级别的日志记录透传到所封装了的处理器中。
- [_SamplingHandler_](../src/Monolog/Handler/SamplingHandler.php): 
  封装另外的处理器，并允许你对日志进行采样，以便你只想存所有日志中的某些（而非全部）。
- [_NoopHandler_](../src/Monolog/Handler/NoopHandler.php): 
  这个处理器对任何东东都不采取任何动作。它不会停止遍历剩余的栈（中的处理器）。
  这个在测试的时候非常有用，还有在想要覆盖掉一个配置以禁用某个处理器的时候。
- [_NullHandler_](../src/Monolog/Handler/NullHandler.php):
  它能处理的任何记录都将被丢弃。这个可以用来放在一个已经存在的处理器栈的栈顶上，以便临时禁用之。
- [_PsrHandler_](../src/Monolog/Handler/PsrHandler.php): 
  可以用来转发日志到一个已经存在了的 PSR-3 日志服务实例中。
- [_TestHandler_](../src/Monolog/Handler/TestHandler.php):
  测试用的，它会记录所有信息，而且有可以读取出这些信息的访问器。
- [_HandlerWrapper_](../src/Monolog/Handler/HandlerWrapper.php): 
  一个简单的处理器的封装器。你可以通过集成它以便更方便地创建你自己的封装器。

## 格式化器

- [_LineFormatter_](../src/Monolog/Formatter/LineFormatter.php): 将日志记录格式化成一行字符串。
- [_HtmlFormatter_](../src/Monolog/Formatter/HtmlFormatter.php): 用来将日志记录格式化成易读的HTML表格，主要适合发邮件。
- [_NormalizerFormatter_](../src/Monolog/Formatter/NormalizerFormatter.php): 将 objects/resources 格式化成字符串，以便更方便地对其进行序列化/编码。
- [_ScalarFormatter_](../src/Monolog/Formatter/ScalarFormatter.php): 用来将日志记录格式化到一个关联数组中，里面都是放标量值。
- [_JsonFormatter_](../src/Monolog/Formatter/JsonFormatter.php): 将日志记录编码成JSON.
- [_WildfireFormatter_](../src/Monolog/Formatter/WildfireFormatter.php): 用来将日志记录格式化成 Wildfire/FirePHP 协议格式，只有对 FirePHPHandler 才有用。
- [_ChromePHPFormatter_](../src/Monolog/Formatter/ChromePHPFormatter.php): 用来将日志格式化成 ChromePHP 格式，只有对 ChromePHPHandler 才有用。
- [_GelfMessageFormatter_](../src/Monolog/Formatter/GelfMessageFormatter.php): 用来将日志记录格式化成 Gelf 消息实例，只有对 GelfHandler 才有用。
- [_LogstashFormatter_](../src/Monolog/Formatter/LogstashFormatter.php): 用来将日志记录格式化成 [logstash](http://logstash.net/) 事件JSON格式，对 [这里](http://logstash.net/docs/latest) 列出的处理器有用。
- [_ElasticaFormatter_](../src/Monolog/Formatter/ElasticaFormatter.php): 用来将日志格式化成 Elastica\Document 对象，只有 ElasticSearchHandler 中才有用。
- [_LogglyFormatter_](../src/Monolog/Formatter/LogglyFormatter.php): 用来将日志格式化成 Loggly 消息，只有对 LogglyHandler 才有用。
- [_FlowdockFormatter_](../src/Monolog/Formatter/FlowdockFormatter.php): 用来将日志格式化成 Flowdock 消息，只有对 FlowdockHandler 才有用。
- [_MongoDBFormatter_](../src/Monolog/Formatter/MongoDBFormatter.php): 将 `\DateTime` 实例转换成 `\MongoDate` 并递归地将对象转换成数组，只有对 MongoDBHandler 才有用。
- [_LogmaticFormatter_](../src/Monolog/Formatter/LogmaticFormatter.php): 用来将日志格式化成 [Logmatic](http://logmatic.io/) 消息，只有对 LogmaticHandler 才有用。

## 加工程序

- [_PsrLogMessageProcessor_](../src/Monolog/Processor/PsrLogMessageProcessor.php): 根据 PSR-3 的规则来处理日志记录，将 `{foo}` 替换成 `$context['foo']`.
- [_IntrospectionProcessor_](../src/Monolog/Processor/IntrospectionProcessor.php): 增加日志调用的时候的行号/文件/类/方法信息。
- [_WebProcessor_](../src/Monolog/Processor/WebProcessor.php): 添加当前请求的 URI, 请求方法和客户端 IP 到日志记录中。
- [_MemoryUsageProcessor_](../src/Monolog/Processor/MemoryUsageProcessor.php): 添加当前的内存使用情况到日志记录中。
- [_MemoryPeakUsageProcessor_](../src/Monolog/Processor/MemoryPeakUsageProcessor.php): 添加峰值内存使用情况到日志记录中。
- [_ProcessIdProcessor_](../src/Monolog/Processor/ProcessIdProcessor.php): 添加进程ID到日志记录中。
- [_UidProcessor_](../src/Monolog/Processor/UidProcessor.php): 添加一个唯一的标识到日志记录中。
- [_GitProcessor_](../src/Monolog/Processor/GitProcessor.php): 添加当前的 git 分支和提交号到日志记录中。
- [_MercurialProcessor_](../src/Monolog/Processor/MercurialProcessor.php): 添加当前的 hg 分支和提交号到日志记录中。
- [_TagProcessor_](../src/Monolog/Processor/TagProcessor.php): 添加一个预定义的标签数组到日志记录中。

## 第三方库


第三方的处理器、格式化器和加工程序的[列表在wiki上](https://github.com/Seldaek/monolog/wiki/Third-Party-Packages). 如果你发布了一个，你也可以往上面添加一条。


&larr; [使用说明](01-usage.md) |  [工具类](03-utilities.md) &rarr;
