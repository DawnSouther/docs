Liquibase 使用手册

---

本文档主要介绍如何在 hap 开发过程中使用 liquibase 来维护数据库数据。

[参考 Liquibase 官方指令文档](http://www.liquibase.org/documentation/changes/index.html)

在 `hap-db` 模块 `com/hand/hap/db/data`目录

有三个版本的数据库脚本：mysql,oracle,sqlserver。

> 旧版的基于 sql 的建表方式已经废弃, 只有第三方的 sql 脚本存在.

## 新建表

---

修改文件 `[yyyy-MM-dd]-init-table-migration.groovy`

> 其他项目上, 应该是根据需要编写至少一个新的 groovy 文件, 命名规则同上.

参考 现有的建表 changeSet 写法, 添加新的 changeSet

注意 `author`,`id`

* 如果是 oracle 数据库, 注意 `sequence` 的创建
* 如果有 tl 表, 写在一个 changeSet 中


其他建表需要注意的内容包括:
* 基础建表语句
* 表描述信息
* 主键设置
* 列描述信息
* 合理的类型，长度
* 是否允许 NULL
* 默认值（如果有）
* 索引（唯一索引，普通索引）
* 主键自增起始 10001
 * oracle 是设置 sequence start with
* who 字段（8个）
```
            column(name: "OBJECT_VERSION_NUMBER", type: "BIGINT", defaultValue : "1")
            column(name: "REQUEST_ID", type: "BIGINT", defaultValue : "-1")
            column(name: "PROGRAM_ID", type: "BIGINT", defaultValue : "-1")
            column(name: "CREATED_BY", type: "BIGINT", defaultValue : "-1")
            column(name: "CREATION_DATE", type: "DATETIME", defaultValueComputed : "CURRENT_TIMESTAMP")
            column(name: "LAST_UPDATED_BY", type: "BIGINT", defaultValue : "-1")
            column(name: "LAST_UPDATE_DATE", type: "DATETIME", defaultValueComputed : "CURRENT_TIMESTAMP")
            column(name: "LAST_UPDATE_LOGIN", type: "BIGINT", defaultValue : "-1")
```
* 扩展字段 16个（可以不要）
> 如果不要，在DTO类上加@ExtensionAttribute(disable=true)

> TL 表均为联合主键，没有自增长、sequence 等

**请注意保持脚本格式工整**


## 修改表结构

 也是通过添加 changeSet, 顺序添加


## 初始数据

---

HAP 2.0 中采用了全新的基于 excel 的基础数据维护.

请参考 `hap-db` 中 com/hand/hap/db/data/2016-09-30-init-data.xlsx

1. 在项目中新建一个 excel 文件. [点击下载模板](http://files.saas.hand-china.com/hap_dev/yyyy-MM-dd-init-data.xlsx.tar.gz)
2. 修改文件 `[yyyy-MM-dd]-init-data-migration.groovy`,添加changeSet,参考写法:

```
 changeSet(author: "jessen", id: "20160930-init-data-xlsx", runAlways:"true"){
        customChange(class:ExcelDataLoader.class.name){
            param(name:"filePath",value:MigrationHelper.getInstance().dataPath("com/hand/hap/db/data/2016-09-30-init-data.xlsx"))
        }
    }
```
excel 数据管理的主要特点有:

* 利用 `公式` 来解决外键依赖问题.
* 可进行 `唯一性检查`, (重复执行, 不会重复插入)
* 数据可更新, 可持续维护

excel 更直观, 便于维护, 所有的基础数据, 只需要维护一份 excel 即可.

> excel 在每次执行 mvn process-resoures ... 时 都会解析执行,
会插入新的数据, 更改已存在的数据.

使用参数 `-Ddb.skipUpdateTables=SYS_ROLE_B,SYS_USER` 可以禁止更新表中的数据（只插入，不更新）

更具体的用法 请参见 excel 第一 sheet 页的说明.


注意：

* 作者
* 时间

> 可以不通过 maven 命令直接执行 excel 导入, 参考代码

```java
    public static void main(String[] args) throws Exception {
        System.setProperty("db.url", "jdbc:mysql://127.0.0.1:3306/hap_dev");
        System.setProperty("db.user", "hap_dev");
        System.setProperty("db.password", "hap_dev");
        System.setProperty("db.override", "true");

        ExcelDataLoader processor = new ExcelDataLoader();
        URL url = ExcelDataLoader.class.getResource("/com/hand/hap/db/data/2016-09-30-init-data.xlsx");
        if (url == null) {
            throw new FileNotFoundException("excel not found.");
        }
        processor.setFilePath(new File(url.toURI()).getAbsolutePath());
        processor.execute(null);
    }

```


## 补丁数据

---

在需要执行比较特殊的数据库操作, 或者不方便归类的操作时 , 可以将脚本写在 patch 中.

patch.groovy 中即可以写 liquibase 命令, 也可以执行 sql 语句.

补丁脚本放在 `patch` 目录。

脚本内容可以包括，但不限于：

* 修改表

* 增删改 表中的数据

> 请注意，这些操作应该都是增量的进行

补丁文件的命名方式：20161212-工号-序号.sql

每次提交都是一个单独的 sql 文件，每天提交多次时，需要指定`序号`加以区分

> 脚本内部注释，写明作者，目的，时间，概要

编写完毕以后，将补丁配置到 patch.groovy 中。

> 补丁将会是用的比较多的方式，主要是增加新的数据，由于主键是手动指定的，请注意不要与他人的重复，必须在本地测试过之后再commit。


