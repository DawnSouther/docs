# MyBatis 使用手册

## 依赖加入

在pom.xml中加入mybatis的相关依赖

```
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis</artifactId>
			<version>3.3.1</version>
		</dependency>
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis-spring</artifactId>
			<version>1.2.4</version>
		</dependency>
```

## 加入配置文件

1. 在web.xml中加入核心配置文件
```
    <context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:spring-mybatis.xml</param-value>
	</context-param>
```
2. 在resource中加入mybatis配置文件,如：

```xml

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xsi:schemaLocation="http://www.springframework.org/schema/beans  
                        http://www.springframework.org/schema/beans/spring-beans-3.1.xsd  
                        http://www.springframework.org/schema/context  
                        http://www.springframework.org/schema/context/spring-context-3.1.xsd  
                        http://www.springframework.org/schema/mvc  
                        http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd">
	<!-- 自动扫描 -->
	<context:component-scan base-package="com.hand" />
	<!-- 引入配置文件 -->
	<bean id="propertyConfigurer"
		class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
		<property name="location" value="classpath:jdbc.properties" />
	</bean>
	<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource"
		destroy-method="close">
		<property name="driverClassName" value="${jdbc.driver}" />
		<property name="url" value="${jdbc.url}" />
		<property name="username" value="${jdbc.username}" />
		<property name="password" value="${jdbc.password}" />
		<!-- 初始化连接大小 -->
		<property name="initialSize" value="${jdbc.initialSize}"></property>
		<!-- 连接池最大数量 -->
		<property name="maxActive" value="${jdbc.maxActive}"></property>
		<!-- 连接池最大空闲 -->
		<property name="maxIdle" value="${jdbc.maxIdle}"></property>
		<!-- 连接池最小空闲 -->
		<property name="minIdle" value="${jdbc.minIdle}"></property>
		<!-- 获取连接最大等待时间 -->
		<property name="maxWait" value="${jdbc.maxWait}"></property>
	</bean>

	<!-- spring和MyBatis完美整合，不需要mybatis的配置映射文件 -->
	<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="dataSource" ref="dataSource" />
		<!-- 自动扫描mapping.xml文件 -->
		<property name="mapperLocations" value="classpath:com/hand/mapping/*.xml"></property>
	</bean>

	<!-- DAO接口所在包名，Spring会自动查找其下的类 -->
	<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
		<property name="basePackage" value="com.hand.dao" />
		<property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"></property>
	</bean>

	<!-- (事务管理)transaction manager, use JtaTransactionManager for global tx -->
	<bean id="transactionManager"
		class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="dataSource" />
	</bean>

</beans>
```

## 搭建mybatis环境

1. 创建实体类
2. 创建数据访问接口（xxxMapper.java）
3. 加入SQL映射语句文件(xxxMapper.xml)

   常见标签
   
| 功能 | 标签名称 |
| -------- | -------- |
| 定义Sql语句   | insert     |
|    |   delete   |
|    |   update   |
|    |   select   |
| 配置java对象属性与查询结果集中列名对应关系   | resultMap   |
| 控制动态sqlpinjie   | foreach   |
|    | if   |
|    |  choose  |
| 格式化输出   | where   |
|    |  set  |
|    |  trim  |
| 配置关联关系   | collection   |
|    | association   |
| 定义常量   | sql   |
| 引用常量   | include   |


**注：** *具体标签使用请查看 ：[mybatis中文教程](http://wenku.baidu.com/link?url=PxezJi5EtQraPdUtiEXRvuOFuE_kWlvCfusPI_5ShNk47Dlj1JEG0rSKnZiRGu5frqU6zgdUG8Fagt7kZ2TC2RVjtqTEICcgbRP433rN5KG
 "mybatis中文教程")*

