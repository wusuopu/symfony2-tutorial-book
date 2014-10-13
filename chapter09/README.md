# Session
这章将介绍Symfony2框架中Session的用法。

## 关于Session
Session相当于是服务器端的Cookie。它是在服务器端存储一些数据，然后将索引的键值作为Cookie值发送给浏览器。

例如打开页面 http://127.0.0.1:8000/ ，可以看到浏览器有条Cookie记录： `PHPSESSID=e42c1bbbf2f709ef8cc7a360a1f3b5de; path=/; domain=127.0.0.1`。其中Cookie名为`PHPSESSID`，值为`e42c1bbbf2f709ef8cc7a360a1f3b5de`。当浏览器发起请求时，服务器端的程序会根据这个值来查找对应的Session。默认是存储在`/tmp/sess_e42c1bbbf2f709ef8cc7a360a1f3b5de`文件中。我们看看该文件的内容：

```
$ cat /tmp/sess_e42c1bbbf2f709ef8cc7a360a1f3b5de
_sf2_attributes|a:2:{s:13:"_csrf/contact";s:43:"HMdTj7H9KrbD2vXslm5H-3rYyWMENM5ZpPBUhWjn5vM";s:32:"_csrf/blogger_blogbundle_comment";s:43:"76koHF_0lGJFFfZTuhOdW4ocjsTlEOLOPDJ0nv97_ZU";}_sf2_flashes|a:0:{}_sf2_meta|a:3:{s:1:"u";i:1413120687;s:1:"c";i:1413084843;s:1:"l";s:1:"0";}
```

它是一个经过了序列化的字符串。

上面说到的Cookie名`PHPSESSID`和Session的存放位置`/tmp`都是在php中进行配置的。查看php的配置信息：

```
$ php -i | grep session
session
session.auto_start => Off => Off
session.cache_expire => 180 => 180
session.cache_limiter => nocache => nocache
session.cookie_domain => no value => no value
session.cookie_httponly => Off => Off
session.cookie_lifetime => 0 => 0
session.cookie_path => / => /
session.cookie_secure => Off => Off
session.entropy_file => /dev/urandom => /dev/urandom
session.entropy_length => 32 => 32
session.gc_divisor => 100 => 100
session.gc_maxlifetime => 1440 => 1440
session.gc_probability => 1 => 1
session.hash_bits_per_character => 4 => 4
session.hash_function => 0 => 0
session.name => PHPSESSID => PHPSESSID
session.referer_check => no value => no value
session.save_handler => files => files
session.save_path => no value => no value
session.serialize_handler => php => php
session.upload_progress.cleanup => On => On
session.upload_progress.enabled => On => On
session.upload_progress.freq => 1% => 1%
session.upload_progress.min_freq => 1 => 1
session.upload_progress.name => PHP_SESSION_UPLOAD_PROGRESS => PHP_SESSION_UPLOAD_PROGRESS
session.upload_progress.prefix => upload_progress_ => upload_progress_
session.use_cookies => On => On
session.use_only_cookies => On => On
session.use_strict_mode => Off => Off
session.use_trans_sid => 0 => 0
```

## Cookie的用法
上面的`PHPSESSID`这条Cookie是由框架自动设置的。现在来说说我们如何设置一条新的Cookie。根据http协议的说明，设置Cookie即是在http的响应头部添加一条`Set-Cookie`指令。

例如现在我们想要为浏览器设置一条名为`cookie1`值为`123456`的Cookie记录。

```php
use Symfony\Component\HttpFoundation\Cookie;

$response->headers->setCookie(new Cookie('cookie1', '123456'));
```

这里的`$response`是一个`Symfony\Component\HttpFoundation\Response`类型的对象，它应该是在控制器中创建的。

然后如果还想要把刚刚这条Cookie删除掉，那么操作如下操作：

```php
$response->headers->clearCookie('test1');
```

## Session的用法
在上面也看到了，Symfony2框架的Session就是将一个PHP数据进行序列化保存的。现在来介绍一下Session的数据设置和读取。

Session相当于一个php数组，直接通过`get`和`set`方法进行读取和写入。例如：

```php
$session = $request->getSession();
$session->set('key', 'value');
$session->get('key');
```

这里先获取`session`对象，在控制器中可以直接通过`$request`对象来获取。也可以通过服务容器`container`来获取：

```php
$session = $container->get('session');
```

其中`$container`是一个`Symfony\Component\DependencyInjection\ContainerInterface`对象。这个在下一章再作介绍。


## 配置Session
上面说过了Cookie名默认为`PHPSESSID`，Session默认是保存在`/tmp`目录下。如果网站同时运行了多个PHP程序，那么Cookie名就会冲突。而且`/tmp`目录下的Session文件也不会自动删除的，这个会导致目录下的文件会越来越多。

针对这两个问题，我们需要修改Symfony2的Session配置参数。编辑文件`app/config/config.yml`，设置`framework -> session -> name`参数，修改Cookie名：

```yml
framework:
    session:
        name: sfblog
```

然后再修改Session的存储方式。Symfony2框架默认提供几种存储后端，保存到SQL数据库、Memcache、MongoDb等。这里我们选择使用SQL数据库。

设置`framework -> session -> handler_id`参数为`session.handler.pdo`，然后再注册一个`session.handler.pdo`服务。最后的配置如下：

```yml
framework:
    session:
        name: sfblog
        handler_id: session.handler.pdo

services:
    pdo:
        class: PDO
        arguments:
            - "mysql:host=%database_host%;port=%database_port%;dbname=%database_name%"
            - "%database_user%"
            - "%database_password%"
        calls:
          - [setAttribute, [3, 2]]    # \PDO::ATTR_ERRMODE, \PDO::ERRMODE_EXCEPTION

    session.handler.pdo:
        class: Symfony\Component\HttpFoundation\Session\Storage\Handler\PdoSessionHandler
        arguments: ["@pdo", { db_table: session, db_id_col: session_id, db_data_col: session_value, db_time_col: session_time } ]
```

这里的`pdo`服务用于配置数据库，同样的对于开发环境我们使用SQLite数据库。编辑文件`app/config/config_dev.yml`，再进行一次配置：

```yml
services:
    pdo:
        class: PDO
        arguments:
            - "sqlite:%kernel.root_dir%/data/data.db3"
        calls:
          - [setAttribute, [3, 2]]    # \PDO::ATTR_ERRMODE, \PDO::ERRMODE_EXCEPTION

    session.handler.pdo:
        class: Symfony\Component\HttpFoundation\Session\Storage\Handler\PdoSessionHandler
        arguments: ["@pdo", { db_table: session, db_id_col: session_id, db_data_col: session_value, db_time_col: session_time } ]
```

上面的配置表明了Session将会保存在数据库的`session`表中。因此我们还得再新建一个数据表，结构如下：

```sql
CREATE TABLE `session` (
    `session_id` varchar(255) NOT NULL,
    `session_value` text NOT NULL,
    `session_time` int(11) NOT NULL,
    PRIMARY KEY (`session_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

不过为了方便，最好还是再新建一个Session实体。通过Doctrine的实体来自动创建表结构。

```
$ php app/console doctrine:generate:entity --entity=BloggerBlogBundle:Session --format=annotation --fields="session_id:string(255) session_value:text session_time:datetime"
```

然后再对文件稍作修改。现在有新的实体了，需要更新数据库结构。可以执行如下命令：

```
$ php app/console doctrine:schema:update --force
```

**注意：上面的执行在更新数据库中可以会导致数据丢失。建议使用`migrations`工具。对于重要的数据可能还需要手动进行迁移。**

在`composer.json`中添加两条依赖：

```
"doctrine/doctrine-migrations-bundle": "dev-master",
"doctrine/migrations": "dev-master",
```

然后`composer.phar update`进行安装。并在`AppKernel`中进行注册`$bundles[] = new Doctrine\Bundle\MigrationsBundle\DoctrineMigrationsBundle();`。

接着生成迁移文件：

```
$ php app/console doctrine:migrations:diff
```

进行迁移：

```
$ php app/console doctrine:migrations:migrate
```



