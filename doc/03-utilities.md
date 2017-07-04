# Utilities

- _Registry_: 这个 `Monolog\Registry` 类允许你配置全局的日志服务实例，然后你就可以从任何地方以静态的方式来访问 Monolog 了。
  这并不是一个最佳实践，不过可以帮你在老的代码库中，或者只是快速方便地，使用 Monolog.
- _ErrorHandler_: 这个 `Monolog\ErrorHandler` 类允许你很方便地注册一个日志服务实例作为一个异常处理器、错误处理器或者致命错误处理器。
- _ErrorLevelActivationStrategy_: 当达到一个特殊日志级别的时候，激活一个 FingersCrossedHandler 
- _ChannelLevelActivationStrategy_: 当达到一个特殊日志级别的时候，激活一个 FingersCrossedHandler, 依赖该日志记录被哪个通道所接收。

&larr; [处理器、格式化器和加工程序](02-handlers-formatters-processors.md) |  [扩展 Monolog](04-extending.md) &rarr;
