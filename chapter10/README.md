# Request && Response && Container
这章将介绍三个常用到的对象：`Request`、`Response`和`Container`。

## Request
`Request`对象是对客户端http请求的封装。我们在使用框架时就要避免再使用原生的PHP方法。

`Request`对象有一些属性保存了请求的信息，如：

* `$request`属性保存了请求的`body`数据，相当于`$_POST`；
* `$query`属性保存了请求的查询字符串，相当于`$_GET`；
* `$server`属性保存了服务器环境信息，相当于`$_SERVER`；
* `$file`属性保存上传的文件，相当于`$_FILES`；
* `$cookies`属性保存了当前的Cookie，相当于`$_COOKIE`；
* `$headers`属性保存了当前的请求头，来自`$_SERVER`。

这些属性都是一个类似于数组类型的对象。

同时`Request`对象还有一些方法，如：`getMethod`、`getPathInfo`、`getClientIp`等。


## Response
`Request`是用户请求来的对象，那么`Response`就是返回给用户的响应对象。每个控制器执行完操作之后都会返回一个`Response`对象。

`Response`对象有一个`$headers`属性，可以用于设置响应请求的头部信息。然后再通过`setContent`方法设置响应请求的主体内容。

Symfony2框架默认提供了以下几种`Response`类，它们都是位于`Symfony\Component\HttpFoundation`命名空间下：

* BinaryFileResponse
* JsonResponse
* RedirectResponse
* Response
* StreamedResponse

## Container
`Container`对象是一个依赖注入的容器，它是一个类似数组类型的对象。服务对象和参数通过键值对的形式保存在它内部。使用它可以在任意地方通过键名访问到服务实体或者参数值。各个模块之间的接口调用可以通过注册服务的形式来完成，这使得程序之间的耦合度降低了。

例如，如果想要操作Session，那么可以通过`Container`获取到Session服务对象实体。

```php
$session = $container->get('session);
```

可以通过如下命令查看当前注册的服务：

```
$ php app/console container:debug
```




