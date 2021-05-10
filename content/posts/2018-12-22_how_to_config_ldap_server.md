---
title: "如何使用openldap搭建LDAP服务篇1--初见LDAP"
categories: [ "服务器技术" ]
tags: [ "centos","Linux","ldap" ]
draft: false
slug: "how_to_config_ldap_server"
date: "2018-12-22 09:13:00"
url: "how_to_config_ldap_server.html"
---

LDAP是轻量目录访问协议，这个服务从老高进入360就一直在用，LDAP带来的好处真的是很多，你可以把它想象成为一个内网版的单点登录服务，可以让你使用同一组账户密码访问所有支持LDAP验证的系统，为企业的内部运维带来了极大的便利。

不过想要自己独立搭建一个LDAP服务还是挺麻烦的！不过一旦搭建好了LDAP服务器，剩下的管理和运维就简单多了。


<!--more-->


在搭建LDAP服务器前，我们必须需要了解一些LDAP的基本概念。


## What is LDAP


Lightweight Directory Access Protocol，轻量目录访问协议，他是基于 X.500(目录访问协议)的DAP发展过来的，与LDAP一样提供类似的目录服务软件还有ApacheDS、Active Directory、Red Hat Directory Service。

> X.500 是 ISO 制定的一套目录服务的标准，它是一个协议族，定义了一个机构如何在全局范围内共享名称和与名称相关联的对象。通过它，可以将局部的目录服务连接起来，构建基于 Internet 的分布在全球的目录服务系统……而目录访问协议（DAP）是 X.500 的核心组成之一。

### LDAP的特点

 - LDAP基于TCP/IP
 - LDAP的结构用树来表示
 - LDAP读快写慢
 - Client/server模型，Server 用于存储数据，Client提供操作目录信息树的工具，LDIF格式
 - LDAP是一种开放Internet标准，LDAP协议是跨平台的Interent协议

### LDAP一些概念

#### 目录树概念

1. 目录树：在一个目录服务系统中，整个目录信息集可以表示为一个目录信息树，树中的每个节点是一个条目。
1. 条目：每个条目就是一条记录，每个条目有自己的唯一可区别的名称（**DN**）。有点类似URI，可以通过一个路径确定唯一一个对象。
1. 对象类：与某个实体类型对应的一组属性，对象类是可以继承的，这样父类的必须属性也会被继承下来。
1. 属性：描述条目的某个方面的信息，一个属性由一个属性类型和一个或多个属性值组成，属性有必须属性和非必须属性。

#### 关键字缩写

| 关键字 | 英文全称 | 含义 |
|-------------|------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Entry | - | 条目，也叫记录项，是LDAP中最基本的颗粒，就像字典中的词条，或者是数据库中的记录。通常对LDAP的添加、删除、更改、检索都是以条目为基本对象的。 |
| Attribute | - | 每个条目都可以有很多属性（Attribute），比如常见的人都有姓名、地址、电话等属性。每个属性都有名称及对应的值，属性值可以有单个、多个，比如你有多个邮箱。 |
| ObjectClass | - | 对象类是属性的集合，LDAP预想了很多人员组织机构中常见的对象，并将其封装成对象类。比如人员（person）含有姓（sn）、名（cn）、电话(telephoneNumber)、密码(userPassword)等属性，单位职工(organizationalPerson)是人员(person)的继承类，除了上述属性之外还含有职务（title）、邮政编码（postalCode）、通信地址(postalAddress)等属性。 |
| Schema | - | 对象类（ObjectClass）、属性类型（AttributeType）、语法（Syntax）分别约定了条目、属性、值，他们之间的关系如下图所示。所以这些构成了模式(Schema)——对象类的集合。条目数据在导入时通常需要接受模式检查，它确保了目录中所有的条目数据结构都是一致的。一般存放路径为`/etc/openldap/schema`，一个条目的属性必须存在于已定义的schema中 |
| LDIF | LDAP Data Interchange Format | LDIF 是一种普遍使用的文件格式，用来描述目录信息或可对目录执行的修改操作。 |
| dc | Domain Component | 域名的部分，其格式是将完整的域名分成几部分，如域名为example.com变成dc=example,dc=com（一条记录的所属位置） |
| uid | User Id | 用户ID songtao.xu（一条记录的ID） |
| ou | Organization Unit | 组织单位，组织单位可以包含其他各种对象（包括其他组织单元），如“oa组”（一条记录的所属组织） |
| cn | Common Name | 公共名称，如“Thomas Johansson”（一条记录的名称） |
| sn | Surname | 姓，如“许” |
| dn | Distinguished Name | “uid=songtao.xu,ou=oa组,dc=example,dc=com”，一条记录的位置（唯一） |
| rdn | Relative dn | 相对辨别名，类似于文件系统中的相对路径，它是与目录树结构无关的部分，如“uid=tom”或“cn= Thomas Johansson” |

#### 简单的例子

请先观察老高画的图，老高预先给出几个dn，看看你能不能总结出出什么规律！
dn:dc=360,dc=Net
dn:ou=Tech,dc=360,dc=Net
dn:uid=phpgao,ou=Web,ou=Tech,dc=360,dc=net

```

                 +-------------+
                 |             |
                 |    dc=Net   <---+ 顶 级 域 名
                 |             |
                 +------+------+
                        |
                        |
                 +------v------+
                 |             <---------+下 一 级 域 名
                 |    dc=360   +-----------+
                 |             |           |
                 +-----+-------+           |
                       |                   |
                       |                   |
                 +-----v------+      +-----v------+
                 |            |      |            |
      +----------+   ou=Tech  |      | ou=Finance <-----+ 组 织
      |          |            |      |            |
      |          +------+-----+      +-----+------+
      |                 |                  |
      |                 |                  |
+-----v-----+    +------v-----+      +-----v------+
|           |    |            |      |            |
|   ou=Web  |    |    ou=App  |      |  uid=cfo   <-----+ 对 象
|           |    |            |      |            |
+-----+-----+    +------+-----+      +------------+
      |                 |
      |                 |
+-----v-----+    +------v-----+
|           |    |            |
| uid=phpgao|    |   uid=dev  |
|           |    |            |
+-----------+    +------------+


```

首先，dn的写法是从下向上，用逗号间隔；
其次，dn是唯一的，否则就没法确定一条entry了；
最后，rdn就是dn最左边的entry。

#### 查看配置文件

##### schema文件

以`/etc/openldap/schema/nis.schema`文件为例，我们可以发现，objectclass **posixAccount** 有以下定义：

```
# Object Class Definitions

objectclass ( 1.3.6.1.1.1.2.0 NAME 'posixAccount'
	DESC 'Abstraction of an account with POSIX attributes'
	SUP top AUXILIARY
	MUST ( cn $ uid $ uidNumber $ gidNumber $ homeDirectory )
	MAY ( userPassword $ loginShell $ gecos $ description ) )
```

我们关注的是MUST中和MAY中的字段，以uidNumber和userPassword()为例，我们可以在文件的`Attribute Type Definitions`节中发现以下定义：

```
# builtin
#attributetype ( 1.3.6.1.1.1.1.0 NAME 'uidNumber'
#	DESC 'An integer uniquely identifying a user in an administrative domain'
#	EQUALITY integerMatch
#	SYNTAX 1.3.6.1.4.1.1466.115.121.1.27 SINGLE-VALUE )


attributetype ( 2.5.4.35 NAME 'userPassword'
	EQUALITY octetStringMatch
	SYNTAX 1.3.6.1.4.1.1466.115.121.1.40{128} )
```


## 安装LDAP(Centos7)

废话不多说，我们先安装好openldap，等下一篇老高来讲讲配置的方法。

### 安装LDAP服务端

```
yum install openldap-servers

# 启动服务

systemctl start slapd
systemctl status slapd

# 查看版本
slapd -V

> OpenLDAP: slapd 2.4.44
```

### 安装LDAP客户端工具

首先，配置好一个新的LDAP服务器后，老高建议优先使用一个GUI工具配置，在此老高推荐使用[Apache Directory Studio][1]，支持多平台配置。


当然，你也可以用命令行管理

```
yum install openldap-clients

# 查看工具
ls -al /usr/bin/ |grep  ldap

ldapadd
ldapcompare
ldapdelete
ldapexop
ldapmodify
ldapmodrdn
ldappasswd
ldapsearch
ldapurl
ldapwhoami
```

## 管理工具

[phpLDAPadmin][2]
[Self Service Password][3]
[JXplorer][4]

Refer:

 - http://www.openldap.org/doc/admin24/
 - https://ldapwiki.com/wiki/Main
 - http://www.zytrax.com/books/ldap/apd/index.html
 - http://www.zytrax.com/books/ldap/ch3/
 - http://www.zytrax.com/books/ldap/ch2/
 - https://www.cnblogs.com/wilburxu/p/9174353.html
 - https://www.adimian.com/blog/2014/10/how-to-enable-memberof-using-openldap/
 - http://seanlook.com/2015/01/15/openldap_introduction/
 - https://www.itzgeek.com/how-tos/linux/centos-how-tos/step-step-openldap-server-configuration-centos-7-rhel-7.html
 - https://linuxhostsupport.com/blog/how-to-install-ldap-on-centos-7/
 - https://www.ibm.com/developerworks/cn/linux/l-openldap/index.html
 - https://www.digitalocean.com/community/tutorials/how-to-change-account-passwords-on-an-openldap-server
 - https://segmentfault.com/a/1190000014683418


  [1]: https://directory.apache.org/studio/downloads.html
  [2]: http://phpldapadmin.sourceforge.net/wiki/index.php/Main_Page
  [3]: https://ltb-project.org/documentation/self-service-password
  [4]: http://jxplorer.org/