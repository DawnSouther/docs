# LDAP服务器搭建

介绍如何在windows平台上安装和配置openLDAP软件。

### 1 下载openLDAP
openLDAP官方提供linux平台的相关安装文件，可在如下网站下载windows版本
* http://www.userbooster.de/download/openldap-for-windows.aspx

### 2 安装openLDAP
按照提示 一直next，直到安装完成
  * 默认端口号389
  * SSL端口号639
  * 默认登录密码secret

> 可以在安装过程中自行修改，或者安装完成后修改配置文件。
  win10下可能会找不到dll文件 ,请下载相关dll文件 放到c盘指定文件

安装完成后，在系统服务中，找到**OpenLDAP Service**，先**停止服务**，再把启动类型修改成**手动**，便于自己的测试。

### 3 配置启动

* 找到安装根路径下的**slapd.conf**文件，找到如下内容

```conf
suffix "dc=maxcrc,dc=com"
rootdn "cn=Manager,dc=maxcrc,dc=com"
```
修改默认管理员账户如下

```conf
suffix "dc=hand,dc=com"
rootdn "cn=Manager,dc=hand,dc=com"
```

* 打开控制台，切换到openLDAP安装目录下，启动openLDAP，命令如下：

```
slapd -d 1
```
控制台出现日志信息：***slapd starting*** 表示服务已经启动好了。

* 在安装根路径下新建demo.ldif，添加一些条目到服务器中，内容如下

```ldif
dn: dc=hand,dc=com
objectclass: domain
objectclass: top
dc: hand

dn: ou=TECH,dc=hand,dc=com
objectclass: organizationalUnit
ou: TECH
description: Container for TECH entries

dn: uid=admin,ou=TECH,dc=hand,dc=com
uid: admin
objectClass: inetOrgPerson
userPassword: admin
sn:firstName
cn:commonName

```
> 格式要严格，每行的开头和末尾不能有空格

> LDAP采用树形结构存储信息，关于创建条目的细节，请自行百度了解
，本文末尾有针对以上数据ldif的简单介绍

然后在控制台中切换到openLDAP安装目录下执行ldapadd命令：

```
ldapadd -x -D "cn=Manager,dc=hand,dc=com" -w secret -f ./demo.ldif

```
> 参数说明

> * -x 使用简单验证方式
* -D 指定管理员DN（与slapd.conf中配置的一致）
* -W 大写W表示回车后根据提示输入密码，可以使用小写的-w
* -f 需要导入数据LDIF的文件名
* -h 目录服务器的IP地址


控制台返回信息添加成功以后，可以自行下载客户端查看具体目录结构。
此时可以按照[开启LDAP验证](ldap-config.md)的步骤，开启LDAP验证，使用admin账户即可以登录到hap系统中。

> 如果遇到问题，可以参考 http://www.micmiu.com/enterprise-app/sso/openldap-windows-config/


### LDAP部分属性名介绍



|  属性名| 描述 |
| -------- | -------- |
| dn | 是该条目在 整个树中的唯一名称标识  |
| dc | 域组件（Domain Component）——一般为LDAP等级结构中的最高一级组织 |
| c | 国家（Country）——一些LDAP等级结构中将国家作为很高的等级 |
| o | 组织名（Organization name）——一个LDAP资源分类上的业务组织|
| ou |	组织单元（Organizational unit）——业务组织部门，一般在组织之内|
|cn|通用名（Common name）——对象的通用名或唯一名或者为对人可读的名字。对人来说一般为人的全名，对于LDAP中的其它资源（电脑等等）一般为主机名。
| uid | 用户ID（User ID）——尽管并不是原生作为组织相关使用，但是Spring一般会查找uid进行用户认证和搜索 |
| userPassword | 用户密码（User password）——存储人对象相关联的密码。一般会经过SHA或类似的单向哈希算法。|

> 有上百个标准的LDAP属性——上面只是其中的一小部分

> objectclass 类似于java中的class ,每个条目都应至少属于一个objectclass,
inetOrgPerson 包含很多用户相关的信息，其中sn 和cn是必要属性。
