# 日志消息的结构


在 Monolog 中日志消息都是以数组的方式来传递的，比如传到加工程序或处理器中。

下面的表格描述了每条日志中总是可用的 key ：

key        | 类型                      | 描述
-----------|---------------------------|-------------------------------------------------------------------------------
message    | string                    | 这条日志消息。当使用`PsrLogMessageProcessor`的时候，这个字符串里面可能会包含占位符，这些占位符可以从对应的上下文中被替换掉。比如：`"User %username% logged in"` 与上下文  `['username' => 'John']` 将被写作 "User John logged in"
level      | int                       | 日志级别（严重程度）. 参见日志 [01-usage.md](01-usage.md) 中描述的日志级别。
level_name | string                    | 字符串形式的日志级别。
context    | array                     | 构造消息的时候传递的任何数据。比如当前用户的用户名或 IP 地址。
channel    | string                    | 发往哪个通道。这个是使用 `new Logger($channel)` 创建日志服务实例的时候传的名字。
datetime   | Monolog\DateTimeImmutable | 日志记录时的日期时间信息。这个类继承了 `\DateTimeImmutable`.
extra      | array                     | 加工程序可以放置额外信息的占位符。总是可用，但是如果没有注册过任何加工程序则为空。

第一眼看上去 `context` 和 `extra` 非常相似，它们都是携带了任意关联了这条日志信息的数据。
他们之间的主要区别在于 `context` 是用户层级上的（它是`Logger::addRecord()`的第三个参数），然而 `extra` 是内部使用的，只能被加工程序所填充。
之所以加工程序使用 `extra` 而不是 `context` 是因为为了防止覆盖 `context` 中用户填充的数据。
