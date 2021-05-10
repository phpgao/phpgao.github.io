---
title: "使用markdown编写简单的接口文档"
categories: [ "代码人生" ]
tags: [ "api","markdown" ]
draft: false
slug: "markdown_api_doc"
date: "2018-05-09 03:28:00"
url: "markdown_api_doc.html"
---

如题，使用markdown格式写接口文档效率还是挺高的，因为你只需要关系接口的功能和内容，而不用在意排版，顺带提一下使用这个工具[markdown_tables][2]创建markdown格式的表格不要太方便！


> 需要说明的是如果是大型多人合作项目，还是老老实实用[apidoc][1]等工具吧。



```
接口大类
-----------
[toc]
-----------

### 接口1

#### 接口功能

> 接口说明

#### 接口地址

> 接口地址

#### 返回格式

> JSON

#### 请求方式

> GET

#### 请求参数

| 参数 | 必选 | 类型   | 默认值 | 说明                                    |
|------|------|--------|--------|-----------------------------------------|
| name | ture | string | foo    | 请求的项目名                            |
| type | true | int    | bar    | 请求项目的类型。1：类型一；2：类型二 。 |

#### 返回字段

| 返回字段 | 字段类型 | 说明                             |
|----------|----------|----------------------------------|
| result   | int      | 返回结果状态。0：正常；1：错误。 |
| reason   | string   | 错误说明                         |
| data     | string   | 数据                             |

#### 字段变化

字段变化说明

#### 接口示例

> 地址：接口地址示例


#```json
{
    "result": 0,
    "reason": "success",
    "data": []
}
#```
```

> 代码里的`#`请删除

  [1]: http://apidocjs.com/
  [2]: http://www.tablesgenerator.com/markdown_tables