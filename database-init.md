# 新建数据库

# 1. 创建数据库-Mysql

> 在开始之前最好修改一下 MySQL的配置文件 my.cnf (or my.ini)
,在 [mysqld] 下添加

```
lower_case_table_names=1
character_set_server=utf8
max_connections=500
```

   * 用管理员登陆mysql
   * 创建数据库hap_dev与hap_prod  
 
   ```sql
   create schema hap_dev default character set utf8;
   create schema hap_prod default character set utf8; 
   ```
   
   > 注意：设置数据库字符集为utf8(utf8 -- UTF-8 Unicode)
   
   * 创建用户hap_dev,并将密码设置为hap_dev  
   
   ```sql
   CREATE USER hap_dev@'%' IDENTIFIED BY 'hap_dev';
   CREATE USER hap_dev@'localhost' IDENTIFIED BY 'hap_dev';
   ```
   * 将上面新建的hap_dev和hap_prod的权限全部赋予用户hap  
   
   ```sql
   GRANT ALL PRIVILEGES ON hap_dev.* TO hap_dev@'%';
   GRANT ALL PRIVILEGES ON hap_dev.* TO hap_dev@'localhost';
   GRANT ALL PRIVILEGES ON hap_prod.* TO hap_dev@'%';
   GRANT ALL PRIVILEGES ON hap_prod.* TO hap_dev@'localhost';

   flush privileges; 
   ```
# 2. 创建数据库-Oracle
  * 使用管理员登陆
  * 创建表空间  
 
  ```sql
  CREATE BIGFILE TABLESPACE tbs_perm_hap
    DATAFILE 'tbs_perm_hap.dat'
      SIZE 200M
      AUTOEXTEND ON NEXT 20M MAXSIZE 500M;  
  ```    
  ```sql
  CREATE TEMPORARY TABLESPACE tbs_temp_hap
    TEMPFILE 'tbs_temp_hap.dbf'
    SIZE 200M
    AUTOEXTEND ON NEXT 20M MAXSIZE 500M;
  ```
   
  * 创建用户 schema   
     
  ```sql
  CREATE USER hap_dev
    IDENTIFIED BY hap_dev
    DEFAULT TABLESPACE tbs_perm_hap
    TEMPORARY TABLESPACE tbs_temp_hap
    QUOTA 200M on tbs_perm_hap;
  ```
    
  * 授权
          
  ```sql  
  GRANT create session TO hap_dev;
  GRANT create table TO hap_dev;
  GRANT create view TO hap_dev;
  GRANT create any trigger TO hap_dev;
  GRANT create any procedure TO hap_dev;
  GRANT create sequence TO hap_dev;
  GRANT create synonym TO hap_dev;
  ```

# 3. 创建数据库-sql server
  * 使用管理员登陆
  * 创建数据库 
 
  ```sql
  CREATE DATABASE hap_dev COLLATE Chinese_PRC_CI_AS;
  ```    
  ```sql
  CREATE DATABASE hap_prod COLLATE Chinese_PRC_CI_AS
  ```
   
  * 创建用户 hap 并授权  
     
  ```sql
  sp_addlogin 'hap','handhapdev';
  use hap_dev
  go
  sp_adduser 'hap','hap'
  go
  sp_addrolemember 'db_owner','hap'
  go
  ```
    
  ```sql
  use hap_prod
  go
  sp_adduser 'hap','hap'
  go
  sp_addrolemember 'db_owner','hap'
  go
  ```