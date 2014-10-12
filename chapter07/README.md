# twig扩展

twig模板引擎默认提供了一些函数和标签，但是有时内置的函数满足不了要求时就需要对twig进行扩展了。

现在我们想让博客的评论时间显示为多久之前发布的，如：发布于3小时前。

之前在模板中渲染发布时间的用法是：`{{ comment.created|date('l, F j, Y') }}`，而现在想变为：`{{ comment.created|created_ago }}`这种形式。

由于twig没有提供`created_ago`方法，因此这得我们自己实现。

新建文件`src/Blogger/BlogBundle/Twig/Extensions/BloggerBlogExtension.php`：

```php
<?php

namespace Blogger\BlogBundle\Twig\Extensions;

/**
 * BloggerBlogExtension
 */
class BloggerBlogExtension extends \Twig_Extension
{
    /**
     * @return array
     */
    public function getFilters()
    {
        return array(
            'created_ago' => new \Twig_Filter_Method($this, 'createdAgo'),
        );
    }

    /**
     * @param \DateTime $dateTime
     *
     * @return string
     */
    public function createdAgo(\DateTime $dateTime)
    {
        $delta = time() - $dateTime->getTimestamp();
        if ($delta < 0) {
            throw new \InvalidArgumentException("createdAgo is unable to handle dates in the future");
        }

        $duration = "";
        if ($delta < 60) {
            // Seconds
            $time = $delta;
            $duration = $time . " second" . (($time > 1) ? "s" : "") . " ago";
        } else if ($delta <= 3600) {
            // Mins
            $time = floor($delta / 60);
            $duration = $time . " minute" . (($time > 1) ? "s" : "") . " ago";
        } else if ($delta <= 86400) {
            // Hours
            $time = floor($delta / 3600);
            $duration = $time . " hour" . (($time > 1) ? "s" : "") . " ago";
        } else {
            // Days
            $time = floor($delta / 86400);
            $duration = $time . " day" . (($time > 1) ? "s" : "") . " ago";
        }

        return $duration;
    }

    /**
     * @return string
     */
    public function getName()
    {
        return 'blogger_blog_extension';
    }
}
```

这里我们定义一个类继承自`Twig_Extension`。要创建自定义过滤器，只需要覆盖`getFilters()`方法即可。如果要创建自定义方法，则覆盖`getFunctions()`方法。

定义了扩展类之后再将其注册为一个服务，编辑文件`src/Blogger/BlogBundle/Resources/config/services.yml`：

```yml
services:
    blogger_blog.twig.extension:
        class: Blogger\BlogBundle\Twig\Extensions\BloggerBlogExtension
        tags:
            - { name: twig.extension }
```

现在就可以在模板中使用我们刚刚定义的`created_ago`过滤器了。更新模板，将`{{ comment.created|date('l, F j, Y') }}`替换成`{{ comment.created|created_ago }}`。

