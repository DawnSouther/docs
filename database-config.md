# Oracle,MySql,Sqlserver,Hana数据库配置

## 配置文件

`3.1.7-RELEASE`以后的版本支持hana数据库

### 1. 数据源配置

   * hap 默认使用 JNDI 来配置数据源。数据源的配置独立于项目配置
   * 默认使用的 JNDI name 为 `jdbc/hap_dev`
   * tomcat 需要配置 context.xml：
     * Mysql 

       ```xml
       <Resource auth="Container" driverClassName="com.mysql.jdbc.Driver" url="jdbc:mysql://localhost:3306/hap_dev" name="jdbc/hap_dev" type="javax.sql.DataSource" username="hap_dev" password="hap_dev"/>
       ```
     * Oracle

       ```xml
       <Resource auth="Container" driverClassName="oracle.jdbc.driver.OracleDriver" name="jdbc/hap_dev" type="javax.sql.DataSource" url="jdbc:oracle:thin:@192.168.115.136:1521:HAP" username="hap_dev" password="hap_dev"/>
       ```

     * SqlServer

       ```xml
       <Resource auth="Container" driverClassName="com.microsoft.sqlserver.jdbc.SQLServerDriver" url="jdbc:sqlserver://10.211.55.6:1433; DatabaseName=hap_dev" name="jdbc/hap_dev" type="javax.sql.DataSource" username="hap" password="handhapdev"/>
       ```

     * Hana 

      ```xml
       <Resource auth="Container" driverClassName="com.sap.db.jdbc.Driver" url="jdbc:sap://192.168.11.28:35215/hap_dev" name="jdbc/hap_dev" type="javax.sql.DataSource" username="hap_dev" password="hap_dev"/>
       ```

   * 其他应用服务器 weblogic 等，需要按其配置方法 配置 JNDI

### 2. 多数据库配置

   > hap 项目 根目录 的 pom.xml 中默认包含了 `Oracle`,`Mysql`,`SqlServer`,`Hana` 的 JDBC 驱动，一般不需要改动

   * 修改  resources/config.properties

     * 当数据库为 oracle 或 hana 时

       ```
       mybatis.identity=SEQUENCE
       ```

     * 修改 db.type

       ```
         db.type=mysql
         # 针对不同的数据库，可选 mysql，oracle，mssql，hana
       ```

     * 修改完毕后复制一份放到 resources/profiles/dev \(覆盖\)
     
   
   * 使用SqlServer 数据库，需要修改  resources/quartz.properties 
      
     ```
        #============================================================================
        # Microsoft SQL Server
        #============================================================================
        org.quartz.jobStore.driverDelegateClass = org.quartz.impl.jdbcjobstore.MSSQLDelegate
        org.quartz.jobStore.selectWithLockSQL=SELECT * FROM {0}LOCKS UPDLOCK WHERE LOCK_NAME = ?
     ```

### 3. ~~liquibase配置文件~~
   * ~~hap-parent/hap/src/java/com/hand/hap/db/liquibase.groovy~~
     * ~~新建项目，请修改 {root}/core/src/java/{package}/db/liquibase.groovy~~
   * ~~需要修改上面文件中的数据库类型 :~~`oracle`~~,~~`mysql`~~,~~`sqlserver`

> 步骤 3 :在 2.0版,2016-10月份开始, 已经不需要了, 现在可以根据 db.url 参数自动识别数据库类型

如果是旧的项目升级，使用`hana`数据库需要修改 {root}/core/src/java/{package}/db/liquibase.groovy

```java
if (dburl.startsWith("jdbc:oracle")) {
        dbType = "oracle";
    } else if (dburl.startsWith("jdbc:sqlserver")) {
        dbType = "sqlserver";
    }else if(dburl.startsWith("jdbc:sap")){
        dbType = "hana";
    }
```

添加自动识别hana数据库类型的代码.

**注意: 以下配置文件项目上如果有自定义需要更新!**

- ``applicationContext.xml``
- ``applicationContext-activiti.xml``

## HANA数据库下liquibase初始化数据库的注意事项：
1.hana数据库使用sequence 生成主键，建表脚本需要加如下判断：

```java
  if(mhi.isDbType('oracle') || mhi.isDbType('hana')){
      createSequence(sequenceName: 'SYS_CODE_B_S', startValue:"10001")
  }

```

2.对于部分liquibase标签不支持，可能需要特殊处理

> 目前发现addNotNullConstraint不支持，解决方法是单独写sql脚本。



