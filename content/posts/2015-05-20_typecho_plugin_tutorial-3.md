---
title: "typecho插件编写教程3---保存配置"
categories: [ "typecho插件" ]
tags: [ "typecho","plugin","tutorial" ]
draft: false
slug: "typecho_plugin_tutorial-3"
date: "2015-05-20 02:45:00"
url: "typecho_plugin_tutorial-3.html"
---

上一节我们制作了一个裸插件，下面我们开始让我们的插件开始工作！


<!--more-->


## 完善方法

### 两个方法

我们实现`activate`和`deactivate`方法

```php
    public static function activate(){
        return 'activate';
    }

    public static function deactivate(){
        return 'deactivated';
    }
```

如上代码所示，我们在激活和卸载插件方法中有返回值，所以在相应操作时会有相应的提示。

![插件启用图][1]

完善信息，使其更接地气

```php
    public static function activate(){
        // do something
        return '插件安装成功，请进入设置填写准入密钥';
    }

    public static function deactivate(){
        // do something
        return '插件卸载成功';
    }
```

## 如何保存配置

准入密钥在哪里保存？当然是数据库了。

typecho已经为我们实现了`Typecho_Widget_Helper_Form`类，我们只需要一点代码就可以摆脱需要自己写表单的烦恼了。

下图是表单类的继承关系，我们可以使用很多类型的表单来保存我们的选项。

![表单类的继承关系][2]

下面我们在`config`方法中保存 **接口调用地址**，类似如下链接（你可以在百度站长平台的**链接提交**里找到）

接口调用地址：http://data.zz.baidu.com/urls?site=www.phpgao.com&token=5wK0QtGCzdRzufvW

```php
    public static function config(Typecho_Widget_Helper_Form $form){
        //保存接口调用地址
        $element = new Typecho_Widget_Helper_Form_Element_Text('api', null, null, _t('接口调用地址'), '请登录百度站长平台获取');
        $form->addInput($element);
    }
```

> 初始化参数有5个，他们都是做什么的？

以下是表单基类的构造方法，他们的功能分别是表单输入项名称、选择项、表单默认值、表单标题、表单描述。

```php

# var/Typecho/Widget/Helper/Form/Element.php:111

    /**
     * 构造函数
     *
     * @access public
     * @param string $name 表单输入项名称
     * @param array $options 选择项
     * @param mixed $value 表单默认值
     * @param string $label 表单标题
     * @param string $description 表单描述
     * @return void
     */
    public function __construct($name = NULL, array $options = NULL, $value = NULL, $label = NULL, $description = NULL)
# 以下省略
```

## 使用需要注意的问题

- 修改表单名称($name)后需要重启插件才能工作，因为插件启用后，表单内容就持久化到数据库中了，禁用插件才能清空该插件的表单设置
- Typecho_Widget_Helper_Form_Element_Fake 不用理会
- 由`var/Widget/Plugins/Edit.php`我们学到很多表单的高级用法，以后老高会在合适的时候提到。



  [1]: https://blog.phpgao.com/usr/uploads/2015/05/247483974.png
  [2]: https://blog.phpgao.com/usr/uploads/2015/05/3193791015.png