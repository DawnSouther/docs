1. 使用 ` git clone -b 2.0 https://rdc.hand-china.com/gitlab/HAP/hap.git hap-parent ` 将项目 clone 到本地
   * -b 参数 指定分支，1.0，2.0 等
2. 确定好数据库后，按照 [多数据库配置](database-config.md) 修改项目配置文件。    
3. 修改配置文件后，按照[创建数据库](database-init.md) 中的步骤创建数据库  


4.  编译整个项目
   - 在 hap-parent 目录下执行：`mvn clean install`
   - 如果不想执行测试, 可以执行 `mvn clean install -Dmaven.test.skip=true`
5. 初始化数据库
   - 在 hap-parent 目录,按照不同的数据库，执行命令：
   - MySql
     - `mvn process-resources -D skipLiquibaseRun=false -D db.driver=com.mysql.jdbc.Driver -D db.url=jdbc:mysql://127.0.0.1:3306/hap_dev -Ddb.user=hap_dev -Ddb.password=hap_dev`
   - SqlServer
     - `mvn process-resources -D skipLiquibaseRun=false  -Ddb.user=hap -Ddb.password=handhapdev -D db.driver=com.microsoft.sqlserver.jdbc.SQLServerDriver -D db.url="jdbc:sqlserver://10.211.55.6:1433; DatabaseName=hap_dev"`
   - Oracle
     - `mvn process-resources -D skipLiquibaseRun=false -D db.driver=oracle.jdbc.driver.OracleDriver -D db.url=jdbc:oracle:thin:@192.168.115.136:1521:HAP -Ddb.user=hap_dev -Ddb.password=hap_dev`
   - Hana
       - `mvn process-resources -D skipLiquibaseRun=false -D db.driver=com.sap.db.jdbc.Driver -D db.url=jdbc:sap://192.168.11.28:35215/hap -Ddb.user=hap_dev -Ddb.password=hap_dev`
     
   - 以上命令中，需要按实际情况修改 url，user，password


6. 运行程序
   - 在 hap-parent 目录,执行: `mvn eclipse:eclipse` 生成 eclipse 配置文件。
   - 将工程导入 eclipse
   - 配置 Server，配置 tomcat，并配置 context JNDI
   - 部署 hap 到 Server
