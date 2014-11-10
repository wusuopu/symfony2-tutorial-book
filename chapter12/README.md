# 日志

在 PHP 程序中进行调试比较常用的方法可能就是直接用 `var_dump` 将内容输出到页面上进行分析。这种方法会将调试信息与页面内容混在一起，一是不便于分析，再者就是可能会影响页面的正常功能。

Symfony2 中使用了 `Monolog` 这个 PHP 的日志库，可以用于将日志记录在服务器端。这样将调试信息和页面内容进行分离。

### 使用方法

框架默认已经注册了一个名为 `logger` 的服务，使用它进行记录日志信息。例如，在控制器中用法如下：

```
    $logger = $this->get('logger');
    $logger->info('I just got the logger');
    $logger->error('An error occurred');
```

以上语句将会向 `"%kernel.logs_dir%/%kernel.environment%.log"` 文件中分别写入级别为 `INFO` 和 `ERROR` 的两行内容。如果是开发环境，那么对应的日志文件为： `app/logs/dev.log` 。


### 自定义日志处理

如果打开 `app/logs/dev.log` 文件会发现它记录了很多内容，这同样不便于我们分析。因此我们需要将我们自己的日志记录到指定的位置。

修改 `app/config/config_dev.yml` 文件，设置 `monolog` 的 `handlers`，新增一项。

```
monolog:
    handlers:
        applog:
            type:   stream
            path:   /tmp/symfony2.log
            level:  info
```

这里我们新建了一个日志处理器，它将只记录 `INFO` 级别以上的日志并保存在 `/tmp/symfony2.log` 文件中。

然后在 `src/Blogger/BlogBundle/Resources/config/services.yml` 文件中进行注册 services：

```
blogger_blog.applog:
    class: Symfony\Bridge\Monolog\Logger
    arguments: ["@logger"]
    tags:
      - { name: monolog.logger, handlers: applog }
```


最后在控制器中执行：

```
    $logger = $this->get('blogger_blog.applog');
    $logger->info('I just got the logger');
    $logger->notice("xxxx");
    $logger->warning("xxxx");
    $logger->error('An error occurred');
```

现在我们自己的日志将保存在 `/tmp/symfony2.log` 文件，这样就与框架自己的日志分开了。

查看 `/tmp/symfony2.log` 文件会发现同样还是存在一些其他的不是我们写入的内容。现在我们想要只记录我们写入的内容，重新修改 `app/config/config_dev.yml` 文件:

```
monolog:
    handlers:
        applog:
            type:   stream
            path:   /tmp/symfony2.log
            level:  info
            channels: [applog]
    channels: ["applog", "main"]
```

为日志设置频道，同时再将刚刚在 `src/Blogger/BlogBundle/Resources/config/services.yml` 文件中注册的 `blogger_blog.applog` 服务删除掉。


最后在控制器中执行：

```
    $logger = $this->get('monolog.logger.applog');
    $logger->info('I just got the logger');
    $logger->notice("xxxx");
    $logger->warning("xxxx");
    $logger->error('An error occurred');
```


参考文档： http://symfony.com/doc/2.5/cookbook/logging/monolog.html

