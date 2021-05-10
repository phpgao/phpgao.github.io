---
title: "如何使用openldap搭建LDAP服务篇2--配置LDAP服务"
categories: [ "服务器技术" ]
tags: [  ]
draft: true
slug: "ldap_server_config"
date: "2018-12-24 08:27:00"
url: "ldap_server_config.html"
---

不区分大小写

修改管理员账户密码

```
slappasswd -h {MD5}

slappasswd -h {SSHA}
```


centos7

```
dn: olcDatabase={2}hdb,cn=config
changetype: modify
replace: olcSuffix
olcSuffix: dc=itzgeek,dc=local

dn: olcDatabase={2}hdb,cn=config
changetype: modify
replace: olcRootDN
olcRootDN: cn=ldapadm,dc=itzgeek,dc=local

dn: olcDatabase={2}hdb,cn=config
changetype: modify
replace: olcRootPW
olcRootPW: {SSHA}d/thexcQUuSfe3rx3gRaEhHpNJ52N8D3
```

centos6

```
dn: olcDatabase={2}bdb,cn=config
changetype: modify
replace: olcSuffix
olcSuffix: dc=lrlz,dc=com

dn: olcDatabase={2}bdb,cn=config
changetype: modify
replace: olcRootDN
olcRootDN: cn=ldapadm,dc=lrlz,dc=com

dn: olcDatabase={2}bdb,cn=config
changetype: modify
replace: olcRootPW
olcRootPW: {SSHA}KwtQ7pYPv/A8st8kaKND7gRNL4kBqb1g
```


执行 ldapmodify -Y EXTERNAL  -H ldapi:/// -f db.ldif



monitor.ldif


```
dn: olcDatabase={1}monitor,cn=config
changetype: modify
replace: olcAccess
olcAccess: {0}to * by dn.base="gidNumber=0+uidNumber=0,cn=peercred,cn=external, cn=auth" read by dn.base="cn=ldapadm,dc=lrlz,dc=com" read by * none
```

ldapmodify -Y EXTERNAL  -H ldapi:/// -f monitor.ldif







cp /usr/share/openldap-servers/DB_CONFIG.example /var/lib/ldap/DB_CONFIG
chown ldap:ldap /var/lib/ldap/*


可选：
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/cosine.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/nis.ldif 
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/inetorgperson.ldif


开启memberof

centos6(64位)

1 memberof_config.ldif

```
dn: cn=module,cn=config
cn: module
objectClass: olcModuleList
olcModuleLoad: memberof
olcModulePath: /usr/lib64/openldap

dn: olcOverlay={0}memberof,olcDatabase={2}bdb,cn=config
objectClass: olcConfig
objectClass: olcMemberOf
objectClass: olcOverlayConfig
objectClass: top
olcOverlay: memberof
olcMemberOfDangling: ignore
olcMemberOfRefInt: TRUE
olcMemberOfGroupOC: groupOfNames
olcMemberOfMemberAD: member
olcMemberOfMemberOfAD: memberOf
```

2 refint1.ldif

```
dn: cn=module{0},cn=config
add: olcmoduleload
olcmoduleload: refint
```

3 refint2.ldif

```
dn: olcOverlay={1}refint,olcDatabase={2}bdb,cn=config
objectClass: olcConfig
objectClass: olcOverlayConfig
objectClass: olcRefintConfig
objectClass: top
olcOverlay: {1}refint
olcRefintAttribute: memberof member manager owner
```

ldapadd -Q -Y EXTERNAL -H ldapi:/// -f memberof_config.ldif
ldapmodify -Q -Y EXTERNAL -H ldapi:/// -f /tmp/refint1.ldif
ldapadd -Q -Y EXTERNAL -H ldapi:/// -f /tmp/refint2.ldif




vi base.ldif

```
dn: dc=lrlz,dc=com
dc: lrlz
objectClass: top
objectClass: domain

dn: cn=ldapadm ,dc=lrlz,dc=com
objectClass: organizationalRole
cn: ldapadm
description: LDAP Manager

dn: ou=People,dc=lrlz,dc=com
objectClass: organizationalUnit
ou: People

dn: ou=Group,dc=lrlz,dc=com
objectClass: organizationalUnit
ou: Group
```



cat person.ldif

```
dn: uid=jianghaomiao,ou=People,dc=lrlz,dc=com
cn: jianghaimiao
mail: haimian.jiang@lrlz.com
sn: jiang
objectClass: inetOrgPerson
userPassword: {MD5}CY9rzUYh03PK3k6DJie09g==

dn: uid=gaozhimin,ou=People,dc=lrlz,dc=com
cn: gaozhimin
mail: zhimin.gao@lrlz.com
sn: gao
objectClass: inetOrgPerson
userPassword: {MD5}CY9rzUYh03PK3k6DJie09g==


dn: uid=huangdong,ou=People,dc=lrlz,dc=com
cn: huangdong
mail: dong.huang@lrlz.com
sn: huang
objectClass: inetOrgPerson
userPassword: {MD5}CY9rzUYh03PK3k6DJie09g==


dn: uid=test,ou=People,dc=lrlz,dc=com
cn: test
mail: test@lrlz.com
sn: test
objectClass: inetOrgPerson
userPassword: {MD5}CY9rzUYh03PK3k6DJie09g==
```
ldapadd -x -D cn=ldapadm,dc=lrlz,dc=com -W -f person.ldif

add group

make add_group.ldif

```
dn: cn=dev,ou=group,dc=lrlz,dc=com
objectClass: groupofnames
cn: dev
description: All users
member: uid=test,ou=people,dc=lrlz,dc=com
```

ldapadd -x -D cn=admin,dc=example,dc=com -W -f add_group.ldif



test

ldapsearch -x -LLL -H ldap:/// -b uid=john,ou=people,dc=example,dc=com dn memberof



