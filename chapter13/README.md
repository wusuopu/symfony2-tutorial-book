# i18n

有时我们需要根据不同语言的用户而显示不同的内容，这就需要需要将内容翻译成多种语言。
在 Symfony2 的框架中提供了一个 `Translation` 的组件可以实现该功能。

参考文档： http://symfony.com/doc/2.5/components/translation/index.html

以下通过一个简单的例子来介绍一下它的用法。

## 使用方法

首先编辑文件 `app/config/parameters.yml` ，设置 `locale` 参数。
这个是用于设置默认的 `locale` ，其值的格式为： 语言代码 + 下划线 + 国家代码。

然后编辑文件 `app/config/config.yml` ，设置 framework ->  translator 参数： { fallback: "%locale%" } 。

其中语言代码遵循 ISO 639-1 标准： http://en.wikipedia.org/wiki/List_of_ISO_639-1_codes  
国家代码遵循 ISO 3166-1 alpha-2 标准： http://en.wikipedia.org/wiki/ISO_3166-1#Current_codes


Symfony2 已经在依赖容器中注册了一个 `translator` 服务。在控制器中可以按照如下方法来使用：

```
    $translated = $this->get('translator')->trans('A message.');
```

在 Twig 模板中可以使用 `trans` 这模板标签或者过滤器。

```
    {% trans %}A memssage.{% endtrans %}
    {{ message|trans }}
```

更新翻译资源文件：

```
$ php app/console translation:update --force zh BloggerBlogBundle
```

该命令会检查模板中的翻译词条，然后更新翻译词条的资源文件。

翻译词条资源文件存放的目录：

    * app/Resources/translations;
    * app/Resources/<bundle name>/translations;
    * 各个 Bundle 目录中的 Resources/translations/.

文件名格式为： domain.locale.loader ，如 messages.fr.yml

这里我们保存到 `src/Blogger/BlogBundle/Resources/translations/messages.zh.yml` 文件中。


然后打开该翻译文件，编辑需要翻译的词条。

最后还需要再更新一下缓存：

```
$ php app/console cache:clear
```


```
实际情况中通常是根据用户浏览器的语言来显示翻译的内容。因此建议的做法是在用户的请求到来时先检查用户的语言，然后再设置到 $request 对象中。

例如：

  $locale = $request->getPreferredLanguage();
  $request->setLocale($locale);

```
