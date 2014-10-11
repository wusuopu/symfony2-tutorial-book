# 简介

Symfony是一款功能强大的PHP框架，最近在折腾它。发现中文资料比较少，当时我是通过symblog这个教程进行学习的。symblog这个教程使用的Symfony版本有些低，有些内容已过时，不过用来学习还是有参考价值的。

本文是在symblog的基础上，并结合自己的笔记整理而来的。本文通过制作一个简单的博客系统来讲解Symfony框架的用法。本文完整的示例代码可以从 https://github.com/wusuopu/symfony2-tutorial-sample 获取到。

Symfony2与Symfony1变化比较大，这里使用的是Symfony2。目前Symfony2的最新版本是2.5。对应的文档为： http://symfony.com/doc/2.5/book/index.html 。

Symfony2的依赖如下：

* PHP版本至少是5.3.3，推荐使用5.5以上的版本；
* PHP开启了JSON支持；
* PHP开启了ctype支持；
* `php.ini`中需要设置`date.timezone`；
* 如果要使用Doctrine还需要安装pdo驱动；

以下是我的PHP信息：

```
$ php -v
PHP 5.5.12 (cli) (built: Aug 24 2014 16:12:16)
Copyright (c) 1997-2014 The PHP Group
Zend Engine v2.5.0, Copyright (c) 1998-2014 Zend Technologies
    with Zend OPcache v7.0.4-dev, Copyright (c) 1999-2014, by Zend Technologies
    with Xdebug v2.3.0dev, Copyright (c) 2002-2013, by Derick Rethans
$ php -i | grep configure
'./configure' '--with-mysql' '--enable-ftp' '--enable-zip' '--with-jpeg-dir' '--with-bz2' '--with-png-dir' '--with-freetype-dir' '--with-iconv' '--with-libxml-dir' '--with-xmlrpc' '--with-zlib-dir' '--with-gd' '--enable-gd-native-ttf' '--with-curl' '--with-gettext' '--with-pear' '--enable-fpm' '--with-ncurses' '--with-mcrypt' '--with-mhash' '--with-openssl' '--with-pcre-dir' '--enable-pdo' '--enable-phar' '--enable-json' '--enable-mbstring' '--with-pdo-mysql' '--with-pdo-sqlite' '--with-readline' '--enable-bcmath' '--enable-intl'
```

如果出现了与本文不一致的情况，可能是软件版本不同的问题。

另外Symfony从JAVA、Rails中借鉴了许多内容，而且现在许多框架都是类似的。因此如果之前有接触过Django、Rails之类的框架的话再入门Symfony应该就不难。

最后作为Pythoner再说一句：“人生苦短，我用Python”。
