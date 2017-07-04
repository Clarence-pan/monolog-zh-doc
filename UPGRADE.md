### 2.0.0

- 时区的配置现在需要在 Logger 实例上来调用，而非静态调用。比如应该通过 `->setTimezone` 或者在构造函数里面传过去。以前调用 `Logger::setTimezone` 的地方需要做下转换。

- 移除了所有非 PSR-3 的添加记录的方法：所有的 `add*` (如 `addWarning`) 方法，包括  `emerg`, `crit`, `err` 和 `warn`.

- `HandlerInterface` 现在被拆成两个接口以便有更细粒度的控制：`ProcessableHandlerInterface`  和
  `FormattableHandlerInterface`. 那些没有继承 `AbstractHandler` 的处理器请确保实现了相关接口。

- `HandlerInterface` 现在需要实现 `close` 方法。这只影响你自己实现的这个接口，不过你应该继承新的 `Monolog\Handler\Handler` 基类。
