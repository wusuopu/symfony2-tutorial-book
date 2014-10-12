# 使用PHPUnit进行单元测试

在开发过程中使用单元测试有助于程序的调试。但是如果像TDD、BDD那样对于每个功能点都要写测试用例又太过于极端了。DHH 说了，TDD 已死。不过对于该测试的还是得测。

首先下载PHPUnit工具： https://phar.phpunit.de/phpunit.phar 。

PHPUnit对应的配置文件为`app/phpunit.xml.dist`，这是个以`.dist`能结尾的文件，可能需要将其复制一份到`app/phpunit.xml`。它配置了一些测试参数，其内容如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>

<!-- http://phpunit.de/manual/4.1/en/appendixes.configuration.html -->
<phpunit xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="http://schema.phpunit.de/4.1/phpunit.xsd"
         backupGlobals="false"
         colors="true"
         bootstrap="bootstrap.php.cache"
>
    <testsuites>
        <testsuite name="Project Test Suite">
            <directory>../src/*/*Bundle/Tests</directory>
            <directory>../src/*/Bundle/*Bundle/Tests</directory>
        </testsuite>
    </testsuites>

    <!--
    <php>
        <server name="KERNEL_DIR" value="/path/to/your/app/" />
    </php>
    -->

    <filter>
        <whitelist>
            <directory>../src</directory>
            <exclude>
                <directory>../src/*/*Bundle/Resources</directory>
                <directory>../src/*/*Bundle/Tests</directory>
                <directory>../src/*/Bundle/*Bundle/Resources</directory>
                <directory>../src/*/Bundle/*Bundle/Tests</directory>
            </exclude>
        </whitelist>
    </filter>
</phpunit>
```

单元测试的目的是独立测试应用程序的个别单元。因此建议单元测试的文件目录结构应与应用程序的一致。例如要测试`src/Blogger/BlogBundle/Controller/PageController.php`的`PageController.php`控制器，那么对应的测试文件为`src/Blogger/BlogBundle/Tests/Controller/PageControllerTest.php`。如下是目录结构示例：

```
src/Blogger/BlogBundle/
        Entity/
            Blog.php
            Comment.php
        Controller/
            PageController.php
        Twig/
            Extensions/
                BloggerBlogExtension.php
        Tests/
            Entity/
                BlogTest.php
                CommentTest.php
            Controller/
                PageControllerTest.php
            Twig/
                Extensions/
                   loggerBlogExtensionTest.php
```


现在我们对`PageCotroller`的`index`方法进行测试，编写测试用例：

```php
// src/Blogger/BlogBundle/Tests/Controller/PageControllerTest.php
class PageControllerTest extends WebTestCase
{
    /**
     * test index page.
     */
    public function testIndex()
    {
        $client = static::createClient();

        $crawler = $client->request('GET', '/');

        $this->assertTrue($crawler->filter('title:contains("symblog")')->count() > 0);
    }
}
```

然后执行单元测试：

```
$ phpunit -c app src/Blogger/BlogBundle/Tests/Controller/PageControllerTest.php
```

这样就可以在代码改动之后通过单元测试来检查当前有没有错误。


关于PHPUnit的使用可以参考它的文档： https://phpunit.de/documentation.html 。



