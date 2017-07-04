# 扩展 Monolog

Monolog 是完全可以扩展的，允许你根据需要调整你的日志服务。

## 理解日志记录

在开始之前，请参考 [这个关于日志记录的页面](message-structure.md)来了解下有哪些东东构成了一条日志记录。
务必要弄清楚日志记录的结构，因为所有的处理器/格式化器/加工程序都需要以某种方式来处理日志记录。

## 编写你自己的处理器

Monolog 提供了许多内建的处理器。不过，如果不存在你所需要的，那么你可以自己写一个然后来用。
唯一的要求就是去实现 `Monolog\Handler\HandlerInterface`.

让我们写个 PDOHandler 来记录日志到数据库中。根据DRY原则，我们将继承 Monolog 提供的那个抽象类 （译注：AbstractProcessingHandler）：

```php
<?php

use Monolog\Logger;
use Monolog\Handler\AbstractProcessingHandler;

class PDOHandler extends AbstractProcessingHandler
{
    private $initialized = false;
    private $pdo;
    private $statement;

    public function __construct(PDO $pdo, $level = Logger::DEBUG, $bubble = true)
    {
        $this->pdo = $pdo;
        parent::__construct($level, $bubble);
    }

    protected function write(array $record)
    {
        if (!$this->initialized) {
            $this->initialize();
        }

        $this->statement->execute(array(
            'channel' => $record['channel'],
            'level' => $record['level'],
            'message' => $record['formatted'],
            'time' => $record['datetime']->format('U'),
        ));
    }

    private function initialize()
    {
        $this->pdo->exec(
            'CREATE TABLE IF NOT EXISTS monolog '
            .'(channel VARCHAR(255), level INTEGER, message LONGTEXT, time INTEGER UNSIGNED)'
        );
        $this->statement = $this->pdo->prepare(
            'INSERT INTO monolog (channel, level, message, time) VALUES (:channel, :level, :message, :time)'
        );

        $this->initialized = true;
    }
}
```

你现在可以在你的日志服务里面使用这个处理器了：

```php
<?php

$logger->pushHandler(new PDOHandler(new PDO('sqlite:logs.sqlite')));

// 记录日志
$logger->info('My logger is now ready');
```

`Monolog\Handler\AbstractProcessingHandler` 类提供了大多数处理器所需要的逻辑，包括
使用加工程序以及格式化日志记录（所有我们直接用 `$record['formatted']` 而非 `$record['message']`）。

&larr; [工具类](03-utilities.md)
