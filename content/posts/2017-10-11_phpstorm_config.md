---
title: "PhpStorm-一些配置"
categories: [ "代码人生" ]
tags: [ "phpstorm","ide" ]
draft: false
slug: "phpstorm_config"
date: "2017-10-11 08:02:00"
url: "phpstorm_config.html"
---

代码写时间长了难免有重装的时候，然后之前一些自定义的东西就没了，主要是一些格式化，模板等一些规范化的东西，之前看好像有配置导出功能，但是用了一次，貌似没什么卵用，所以还是记下来吧。


<!--more-->


## APIdoc


> Settings (Preferences on Mac) | Editor | File and Code Templates | PHP Function Doc Comment

默认：

```
/**
${PARAM_DOC}
#if (${TYPE_HINT} != "void") * @return ${TYPE_HINT}
#end
${THROWS_DOC}
*/
```

下面是一些说明

```
A built-in template for PHP function doc comment. 
Predefined variables will take the following values:
${NAME}
 
Function name.
${TYPE_HINT}
 
A type hint of the function's return value. If no return types can be found from function static analysis, evaluates to "void".
${PARAM_DOC}
 
Parameters' doc comment.
Generated as a number of lines '* @param type name". If there are no parameters, evaluates to an empty content.
${THROWS_DOC}
 
Exceptions' doc comment.
Generated as a number of lines '* @throws type". If there are no thrown exceptions, evaluates to an empty content.
${STATIC}
 
Takes a value of "static" if the function (method) is static or an empty string otherwise. For example: 
      #if (${STATIC} == "static") * @static      #end    
${DS}
 
Dollar sign, evaluates to a plain '$' character.
${CARET}
 
Marks a position where the caret must be moved after the comment is added.
```


## ARRAY对齐

> Settings (Preferences on Mac) | Editor | Code Style | PHP | Other | Array declaration style -> Align key-value pairs

```php
$arr = [
   "foo" => "bar",
   "barr" => "foo",
   "bar_____r" => "foo",
]


$arr = [
   "foo"       => "bar",
   "barr"      => "foo",
   "bar_____r" => "foo",
]
```

## 赋值对齐

> Settings (Preferences on Mac) | Editor | Code Style | PHP | Wrapping and Braces -> Assignment Statement


```php
$sExample = 'Example !';
$sAnotherOneHere = 'Again ?';
$sAndTheLast = 'ENOUGH !';

$sExample        = 'Example !';
$sAnotherOneHere = 'Again ?';
$sAndTheLast     = 'ENOUGH !';
```


## 参数类型提示

关闭以提高性能

> Settings->Editor->General->Appearance->"Show parameter name hints"

-----

待补充