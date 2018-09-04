# 数据多语言

## 表结构(以sys_role为例)
### 主表
多语言基表以`_B` 结尾,与非多语言的表没有大的差别
```groovy
createTable(tableName: "SYS_ROLE_B"){
    column(name:"ROLE_ID",type:"bigint",autoIncrement: "true", startWith:"10001",remarks: "表ID，主键"){
        constraints(nullable: "false", primaryKey: "true",primaryKeyName: "SYS_ROLE_B_PK")
    }
    column(name:"ROLE_CODE",type:"varchar(40)",remarks: "角色编码"){
        constraints(nullable: "false",unique: "true",uniqueConstraintName: "SYS_ROLE_B_U1")
    }
    column(name:"ROLE_NAME",type:"varchar(150)",remarks: "角色名称"){
        constraints(nullable: "false")
    }
    column(name:"ROLE_DESCRIPTION",type:"varchar(240)",remarks: "角色描述")
    column(name:"START_ACTIVE_DATE",type:"DATE",remarks:"开始生效日期")
    column(name:"END_ACTIVE_DATE",type:"DATE",remarks:"截至生效日期")
    column(name:"ENABLE_FLAG",type:"VARCHAR(1)",remarks:"启用标记", defaultValue: "Y")
    ...
}
```
### 多语言表

多语言表以`_TL` 结尾,主要包含以下字段:

- 基表主键
- LANG
- 多语言字段

```groovy
createTable(tableName:"SYS_ROLE_TL") {
    column(name: "ROLE_ID", type: "bigint", remarks: "角色ID") {
        constraints(nullable: "false", primaryKey: "true")
    }
    column(name: "LANG", type: "varchar(10)", remarks: "语言") {
        constraints(nullable: "false", primaryKey: "true")
    }
    column(name: "ROLE_NAME", type: "varchar(150)", remarks: "角色名称")
    column(name: "ROLE_DESCRIPTION", type: "varchar(240)", remarks: "角色描述")
    ...
}
```

## DTO
### 多语言注解

* MultiLanguage

多语言主表对应的 DTO 需要添加 MultiLanguage 注解

```java
@MultiLanguage
@Table(name = "sys_role_b")
public class Role extends BaseDTO implements IRole {
  ...
}
```

* MultiLanguageField
多语言字段需要添加 MultiLanguageField注解

```java
@MultiLanguage
@Table(name = "sys_role_b")
public class Role extends BaseDTO implements IRole {

    @MultiLanguageField
    private String name;

    @MultiLanguageField
    private String description;
}
```

## 说明

1. HAP 中多语言`新增`,`更新`,`删除` 逻辑是通过 MyBatis 的 Interceptor 来实现的，具体可参考实现类 `MultiLanguageInterceptor`
2. HAP 根据 DTO 中的注解自动生成查询 SQL 关联多语言表，具体相关类 `SqlHelper`
