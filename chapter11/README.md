# 网站部署
至此已经介绍了Symfony2框架的基本用法，包括：

  * 路由
  * 表单
  * 数据库
  * Cookie
  * Session
  * 模板
  * Request
  * Response

这些都是基本的内容，对于现在Web开发来说都会用到。现在博客程序也基本成形了，准备在服务器上进行部署。这里我们选择nginx + php + fastcgi 的组合。

先启动php的fastcgi程序php-fpm。

然后配置nginx服务，以下是一个配置示例：

```
server {
    server_name domain.tld www.domain.tld;
    root /var/www/project/web;

    location / {
        # try to serve file directly, fallback to app.php
        try_files $uri /app.php$is_args$args;
    }

    location ~ ^/(app|app_dev|config)\.php(/|$) {
        fastcgi_pass unix:/var/run/php5-fpm.sock;
        fastcgi_split_path_info ^(.+\.php)(/.*)$;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }

    error_log /var/log/nginx/project_error.log;
    access_log /var/log/nginx/project_access.log;
}
```

接着启动nginx服务即可。如果程序用到了MySQL数据库，那么还需要再配置MySQL。

现在本篇教程的基本篇已经介绍完了，从下一章开始将介绍进阶篇。进阶篇的内容基本上都是从Symfony2的CookBook中整理出来的，有的不一定会用到，不过有时候大部分都是比较实用的。


