---
title: "typecho插件编写教程7---Helper类"
categories: [ "代码人生" ]
tags: [ "typecho","plugin","tutorial" ]
draft: false
slug: "typecho_plugin_tutorial-7"
date: "2015-06-02 03:00:00"
url: "typecho_plugin_tutorial-7.html"
---

`Helper`类为我们封装了很多与插件有关的操作，并且全部是公共静态方法，比如获取系统配置、添加路由、添加面板等功能，是开发插件必不可少的工具。

> 插件帮手将默认出现在所有的typecho发行版中.因此你可以放心使用它的功能, 以方便你的插件安装在用户的系统里.

Helper类的注释已经写的很清楚了，老高在此仅挑几个常用的方法讲讲。

<!--more-->

## 获取配置

```php
# 获取系统配置
Helper::options();

# 获取指定插件配置
Helper::options()->plugin('BaiduSubmit');

# 获取当前插件配置
Helper::options()->plugin(str_replace('_Plugin','', __CLASS__));
```

## 路径安全

用于生成安全的URL，带有token验证，如果牵扯到action等核心操作时会用到。系统在验证请求是会用到`$this->widget('Widget_Security')->protect()`去验证来源token。

```php
$s = Helper::security();
echo $s->index('/');

# 输出类似 https://blog.phpgao.com/?_=xxx
```

## 强行删除某插件

此方法用于卸载插件失败时的替补方法，老高一般将此方法写入`HELLO_WORLD`插件的`render`方法里，这样刷新以下后台，出问题的插件就被卸载了。

```
Helper::removePlugin('BaiduSubmit');
```

## 路由增删

如果你的插件需要给系统添加一个新的路由，那么此方法就能派上用场！

> 路由表保存在表 `typecho_options` 中的 `routingTable`中。

比如老高的插件[BaiduSubmit][1]，就用此方法为系统添加了一个`http://yourweb/baidu_sitemap.xml`的路由。

```
/**
 * 增加路由
 *
 * @access public
 * @param string $name 路由名称
 * @param string $url 路由路径
 * @param string $widget 组件名称
 * @param string $action 组件动作
 * @param string $after 在某个路由后面
 * @return integer
 */
Helper::addRoute('baidu_sitemap', '/baidu_sitemap.xml', 'BaiduSubmit_Action', 'sitemap');
```

> 不要忘记在卸载的时候调用`Helper::removeRoute('baidu_sitemap');`删除刚才添加的路由！

## Action

有心的同学应该能够发现，后台发送文章的form表单的action地址为类似`http://web/action/contents-post-edit?_=xxx`，这个链接表示什么意思呢？请听老高分解！

Action是typecho中的一类操作，它对应一条特殊的路由/action/，也就是路由表中的`$table[0]['do']`，具体内容如下

```php
[do] => Array
(
    [url] => /action/[action:alpha]
    [widget] => Widget_Do
    [action] => action
    [regx] => |^/action/([_0-9a-zA-Z-]+)[/]?$|
    [format] => /action/%s
    [params] => Array
        (
            [0] => action
        )

)
```

从路由表能够看出，下一步我们需要找到类`Widget_Do`。我们能在该类中找到系统默认的action对应关系！

```php
    private $_map = array(
        'ajax'                      =>  'Widget_Ajax',
        'login'                     =>  'Widget_Login',
        'logout'                    =>  'Widget_Logout',
        'register'                  =>  'Widget_Register',
        'upgrade'                   =>  'Widget_Upgrade',
        'upload'                    =>  'Widget_Upload',
        'service'                   =>  'Widget_Service',
        'xmlrpc'                    =>  'Widget_XmlRpc',
        'comments-edit'             =>  'Widget_Comments_Edit',
        'contents-page-edit'        =>  'Widget_Contents_Page_Edit',
        'contents-post-edit'        =>  'Widget_Contents_Post_Edit',
        'contents-attachment-edit'  =>  'Widget_Contents_Attachment_Edit',
        'metas-category-edit'       =>  'Widget_Metas_Category_Edit',
        'metas-tag-edit'            =>  'Widget_Metas_Tag_Edit',
        'options-discussion'        =>  'Widget_Options_Discussion',
        'options-general'           =>  'Widget_Options_General',
        'options-permalink'         =>  'Widget_Options_Permalink',
        'options-reading'           =>  'Widget_Options_Reading',
        'plugins-edit'              =>  'Widget_Plugins_Edit',
        'themes-edit'               =>  'Widget_Themes_Edit',
        'users-edit'                =>  'Widget_Users_Edit',
        'users-profile'             =>  'Widget_Users_Profile'
    );
```

由此映射关系我们知道，当我们需要发布文章的时候（contents-post-edit），就去调用`Widget_Contents_Post_Edit`的action方法即可。

当我们要添加action对应关系的时候，就可以调用`Helper:addAction()`执行添加，系统就会添加一条`'new_action' => 'some_action'`。

> 新增的动作保存在表 `typecho_options` 中的 `actionTable`中。

需要提醒的是，当我们添加一条action后，需要让其实现类`some_action`实现`Widget_Interface_Do`的接口，即必须有实现`public function action()`，否则调回失败。

> 同route一样，在卸载插件时不要忘了移除action！

## 菜单 & 面板

这个很好理解，不再赘述。

## 手动配置

当我们需要更自由的配置功能时，可以在Plugin.php文件中添加`public static function configHandle($config)`方法，当后台保存插件设置的时候就会调用此方法而不是默认的简单保存配置。

```php
Helper::configPlugin('BaiduSubmit', $config);
```

完！

  [1]: https://blog.phpgao.com/typecho_plugin_baidusubmit.html