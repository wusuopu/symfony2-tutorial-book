# 创建项目

准备工作已做好了，接下来就开始创建一个新的项目。创建项目有两种方法：使用Composer、下载软件包。

不管是用哪种方法都需要用到`Composer`，因此我们需要先安装`Composer`。`Composer`是PHP的一个包管理工具，类似于Python的`pip`。

### 安装Composer

安装方法也很简单，执行如下命令进行下载安装，并将`Composer`程序移动到 `/usr/local/bin` 目录便于之后使用。

```shell
$ curl -s https://getcomposer.org/installer | php
$ [sudo] mv composer.phar /usr/local/bin
```

### 通过Composer创建项目

Composer安装完成之后，即可通过如下命令创建一个新的Symfony2项目，然后根据提示进行配置。

```shell
$ composer.phar create-project symfony/framework-standard-edition symfony_tutorial 2.5.*
Installing symfony/framework-standard-edition (v2.5.5)
  - Installing symfony/framework-standard-edition (v2.5.5)
    Downloading: 100%

Created project in symfony_tutoriial
Loading composer repositories with package information
Installing dependencies (including require-dev)
  - Installing jdorn/sql-formatter (v1.2.17)
    Loading from cache

  - Installing psr/log (1.0.0)
    Loading from cache

  - Installing twig/twig (v1.16.0)
    Loading from cache

  - Installing doctrine/lexer (v1.0)
    Loading from cache

  - Installing doctrine/annotations (v1.2.1)
    Downloading: 100%

  - Installing doctrine/collections (v1.2)
    Loading from cache

  - Installing doctrine/cache (v1.3.1)
    Loading from cache

  - Installing doctrine/inflector (v1.0)
    Loading from cache

  - Installing doctrine/common (v2.4.2)
    Loading from cache

  - Installing symfony/symfony (v2.5.5)
    Downloading: 100%

  - Installing symfony/icu (v1.2.2)
    Downloading: 100%
    Downloading: 100%

  - Installing doctrine/dbal (v2.4.2)
    Loading from cache

  - Installing doctrine/doctrine-bundle (v1.2.0)
    Loading from cache

  - Installing kriswallsmith/assetic (v1.1.2)
    Loading from cache

  - Installing symfony/assetic-bundle (v2.3.0)
    Loading from cache

  - Installing doctrine/orm (v2.4.6)
    Downloading: 100%

  - Installing twig/extensions (v1.1.0)
    Loading from cache

  - Installing swiftmailer/swiftmailer (v5.3.0)
    Downloading: 100%

  - Installing symfony/swiftmailer-bundle (v2.3.7)
    Loading from cache

  - Installing monolog/monolog (1.11.0)
    Downloading: 100%

  - Installing symfony/monolog-bundle (v2.6.1)
    Loading from cache

  - Installing sensiolabs/security-checker (v2.0.0)
    Downloading: 100%

  - Installing sensio/distribution-bundle (v3.0.6)
    Downloading: 100%

  - Installing sensio/framework-extra-bundle (v3.0.2)
    Loading from cache

  - Installing incenteev/composer-parameter-handler (v2.1.0)
    Loading from cache

  - Installing sensio/generator-bundle (v2.4.0)
    Downloading: 100%

kriswallsmith/assetic suggests installing leafo/lessphp (Assetic provides the integration with the lessphp LESS compiler)
kriswallsmith/assetic suggests installing leafo/scssphp (Assetic provides the integration with the scssphp SCSS compiler)
kriswallsmith/assetic suggests installing ptachoire/cssembed (Assetic provides the integration with phpcssembed to embed data uris)
kriswallsmith/assetic suggests installing leafo/scssphp-compass (Assetic provides the integration with the SCSS compass plugin)
monolog/monolog suggests installing graylog2/gelf-php (Allow sending log messages to a GrayLog2 server)
monolog/monolog suggests installing raven/raven (Allow sending log messages to a Sentry server)
monolog/monolog suggests installing doctrine/couchdb (Allow sending log messages to a CouchDB server)
monolog/monolog suggests installing ruflin/elastica (Allow sending log messages to an Elastic Search server)
monolog/monolog suggests installing videlalvaro/php-amqplib (Allow sending log messages to an AMQP server using php-amqplib)
monolog/monolog suggests installing ext-amqp (Allow sending log messages to an AMQP server (1.0+ required))
monolog/monolog suggests installing ext-mongo (Allow sending log messages to a MongoDB server)
monolog/monolog suggests installing aws/aws-sdk-php (Allow sending log messages to AWS services like DynamoDB)
monolog/monolog suggests installing rollbar/rollbar (Allow sending log messages to Rollbar)
Writing lock file
Generating autoload files
Would you like to install Acme demo bundle? [y/N] y
Creating the "app/config/parameters.yml" file
Some parameters are missing. Please provide them.
database_driver (pdo_mysql):
database_host (127.0.0.1):
database_port (null):
database_name (symfony):
database_user (root):
database_password (null):
mailer_transport (smtp):
mailer_host (127.0.0.1):
mailer_user (null):
mailer_password (null):
locale (en):
secret (ThisTokenIsNotSoSecretChangeIt):
debug_toolbar (true):
debug_redirects (false):
use_assetic_controller (true):
Clearing the cache for the dev environment with debug true
Installing assets as hard copies
Installing assets for Symfony\Bundle\FrameworkBundle into web/bundles/framework
Installing assets for Sensio\Bundle\DistributionBundle into web/bundles/sensiodistribution
```

### 通过下载软件包创建项目

从 http://symfony.com/download?v=Symfony_Standard_Vendors_2.5.5.zip 下载软件压缩包，然后解压即可。

```
$ unzip Symfony_Standard_Vendors_2.5.5.zip
```


不管是用哪种方法进行安装的，最终项目的目录结构如下：

```
project_root/
    app/            <- 应用程序配置
        cache/
        config/
        logs/
    src/            <- 项目代码
        ...
    vendor/         <- 第三方依赖
        ...
    web/            <- web根目录
        app.php
        app_dev.php
        ...
    composer.json
```

其中 `vendor` 目录是存放第三方依赖库的，如果使用的`git`进行代码的版本管理的话，应该要把它添加到`.gitignore`文件中。

如果你的项目中 `vendor` 目录为空，那么需要执行命令 `composer.phar install` 进行安装第三方依赖库。如果第三方依赖库有更新，那么需要执行命令 `composer.phar update` 进行更新。


## 更新配置

项目创建完成之后，接下来是检查环境是否正确以及更新配置信息。

先执行如下命令进行检查：

```shell
$ php app/check.php

Symfony2 Requirements Checker
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

> PHP is using the following php.ini file:
  /opt/lcphp/lib/php.ini

> Checking Symfony requirements:
  ........................................


 [OK]
 Your system is ready to run Symfony2 projects


Note  The command console could use a different php.ini file
~~~~  than the one used with your web server. To be on the
      safe side, please check the requirements from your web
      server using the web/config.php script.
```

环境检查没有问题之后再进行编辑 `app/config/parameters.yml` 文件，配置数据库信息和邮件信息。


