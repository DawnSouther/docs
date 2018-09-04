# 多数源配置

---

HAP框架的多数据源配置：

* 单独配置多个数据源，配置不同的sessionFactory，mapperScannerConfigurer。根据不同业务自行选择使用哪个数据源，针对每一个数据源，单独配置transactionManager。

下面贴出一个配置demo:

```xml
<!-- jndi dataSource-->
 <bean id="dataSource" class="org.springframework.jndi.JndiObjectFactoryBean">
      <property name="jndiName" value="${db.jndiName}"/>
  </bean>
  <bean id="dataSource2" class="org.springframework.jndi.JndiObjectFactoryBean">
      <property name="jndiName" value="${db.jndiName2}"/>
  </bean>

  <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <!-- 注意：这里针对不同的数据源，应该配置不同的扫描路径！！！-->
        <property name="mapperLocations" value="classpath*:com/hand/hap/**/*Mapper.xml"/>
        <property name="plugins">
            <array>
                <bean class="com.hand.hap.core.interceptor.RequestContextInterceptor"/>
                <bean class="com.hand.hap.core.interceptor.MultiLanguageInterceptor"/>
                <bean class="com.hand.hap.core.interceptor.SecurityTokenInterceptor"/>
                <bean class="com.hand.hap.core.interceptor.OvnInterceptor"/>
                <bean class="com.hand.hap.core.interceptor.AuditInterceptor"/>
                <bean class="com.hand.hap.core.interceptor.DataPermissionInterceptor"/>
                <bean class="com.github.pagehelper.PageHelper"/>
                <bean class="com.hand.hap.core.interceptor.CacheJoinInterceptor">
                    <property name="cacheJoinType" ref="cacheJoinType"></property>
                </bean>
            </array>
        </property>
        <property name="configLocation" value="classpath:mybatis-configuration.xml"/>
    </bean>
    <bean id="sqlSessionFactory2" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource2"/>
        <!-- 注意：这里针对不同的数据源，应该配置不同的扫描路径！！！-->
        <property name="mapperLocations" value="classpath*:com/hand/test/**/*Mapper.xml"/>
        <property name="plugins">
            <array>
                <bean class="com.hand.hap.core.interceptor.RequestContextInterceptor"/>
                <bean class="com.hand.hap.core.interceptor.MultiLanguageInterceptor"/>
                <bean class="com.hand.hap.core.interceptor.SecurityTokenInterceptor"/>
                <bean class="com.hand.hap.core.interceptor.OvnInterceptor"/>
                <bean class="com.hand.hap.core.interceptor.AuditInterceptor"/>
                <bean class="com.hand.hap.core.interceptor.DataPermissionInterceptor"/>
                <bean class="com.github.pagehelper.PageHelper"/>
                <bean class="com.hand.hap.core.interceptor.CacheJoinInterceptor">
                    <property name="cacheJoinType" ref="cacheJoinType"></property>
                </bean>
            </array>
        </property>
        <property name="configLocation" value="classpath:mybatis-configuration.xml"/>
    </bean>

    <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
        <constructor-arg index="0" name="sqlSessionFactory" ref="sqlSessionFactory"/>
    </bean>

    <!-- mapper definition -->
    <bean id="mapperScannerConfigurer" class="com.hand.hap.mybatis.spring.MapperScannerConfigurer">
        <!-- 注意：这里针对不同的数据源，应该配置不同的扫描路径！！！-->
        <property name="basePackage" value="com.hand.hap.**.mapper"/>
        <property name="processPropertyPlaceHolders" value="true"/>
        <property name="propertiesMap">
            <map>
                <entry key="mappers" value="com.hand.hap.mybatis.common.Mapper"/>
                <entry key="IDENTITY" value="${mybatis.identity}"/>
                <entry key="dataBaseType" value="${db.type}"/>
                <entry key="seqFormat" value="{3}_s.nextVal"/>
                <entry key="enableMethodAnnotation" value="true"/>
            </map>
        </property>
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
    </bean>

    <bean id="mapperScannerConfigurer2" class="com.hand.hap.mybatis.spring.MapperScannerConfigurer">
      <!-- 注意：这里针对不同的数据源，应该配置不同的扫描路径！！！-->
      <property name="basePackage" value="com.hand.test.**.mapper"/>
      <property name="processPropertyPlaceHolders" value="true"/>
      <property name="propertiesMap">
        <map>
            <entry key="mappers" value="com.hand.hap.mybatis.common.Mapper"/>
            <entry key="IDENTITY" value="${mybatis.identity2}"/>
            <entry key="dataBaseType" value="${db.type2}"/>
            <entry key="seqFormat" value="{3}_s.nextVal"/>
            <entry key="enableMethodAnnotation" value="true"/>
        </map>
    </property>
    <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory2"/>
</bean>

<!-- config transactionManager -->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
     <property name="dataSource" ref="dataSource"/>
 </bean>
<bean id="transactionManager2" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource2"/>
</bean>
 <tx:annotation-driven/>
```

配置完成后，因为不同的数据源配置的mapper扫描路径不一样，所以根据包名可以区分使用的数据源。

> 注意 不同的transactionManager管理不同的数据源，在service层的Transactional注解上需要指定transactionManager的ID，默认不指定是用transactionManager

```Java
package com.hand.test.test.service.impl;

@Service
@Transactional(value = "transactionManager2",rollbackFor = Exception.class)
public class DemoServiceImpl extends BaseServiceImpl<Demo> implements IDemoService{

    @Autowired
    private IUserService userService;

    @Override
    public Demo insertSelective(IRequest request, Demo record) {
        Demo demo =  super.insertSelective(request, record);
        userService.insertSelective(request,new User());
        //throw Exception
        int i = 1/0;
        return demo;
    }
}
```

但是以上代码有个问题,因为IUserService使用的是dataSource，但是事务管理器transactionManager2管理的是dataSource2，因此抛出异常后，userService插入的数据不会回滚，插入的demo则会回滚。

所以使用这个配置，要小心业务代码里同时使用多个数据源。  
如果你想保证事务一致性，抛出异常后，两个数据源都可以一致回滚，可以采用Atomikos，一个分布式事务的解决方案，下面给出配置demo:

首先在pom里添加如下依赖：

```xml
<dependency>
           <groupId>com.atomikos</groupId>
           <artifactId>transactions-jdbc</artifactId>
           <version>4.0.2</version>
       </dependency>
       <dependency>
           <groupId>javax.transaction</groupId>
           <artifactId>jta</artifactId>
           <version>1.1</version>
</dependency>
```

配置数据源：

```xml
<!-- 两个数据源的功用配置，方便下面直接引用 -->
  <!-- 数据源配置必须使用AtomikosDataSourceBean，因此JNDI配置不能用了-->
  <bean id="abstractXADataSource" class="com.atomikos.jdbc.AtomikosDataSourceBean" init-method="init"
        destroy-method="close" abstract="true">
      <property name="poolSize" value="10" />
      <property name="minPoolSize" value="10"/>
      <property name="maxPoolSize" value="30"/>
      <property name="borrowConnectionTimeout" value="60"/>
      <property name="reapTimeout" value="20"/>
      <!-- 最大空闲时间 -->
      <property name="maxIdleTime" value="60"/>
      <property name="maintenanceInterval" value="60" />
      <property name="loginTimeout" value="60"/>
      <property name="logWriter" value="60"/>
     <!-- <property name="testQuery">
          <value>select 1</value>
      </property>-->

  </bean>

  <!-- 配置第一个数据源 -->
  <bean id="dataSource" parent="abstractXADataSource">
      <!-- value只要两个数据源不同就行，随便取名 -->
      <property name="uniqueResourceName" value="oracle/sitestone" />
      <!-- 注意className,不同的数据库不一样 -->
      <property name="xaDataSourceClassName" value="oracle.jdbc.xa.client.OracleXADataSource"/>
      <property name="xaProperties">
          <props>
              <prop key="URL">jdbc:oracle:thin:@10.211.107.202:1521:xe</prop>
              <prop key="user">hap_dev</prop>
              <prop key="password">hap_dev</prop>
          </props>
      </property>
  </bean>

  <!-- 配置第二个数据源-->
  <bean id="dataSource2" parent="abstractXADataSource">
      <!-- value只要两个数据源不同就行，随便取名 -->
      <property name="uniqueResourceName" value="mysql/sitesttwo" />
      <property name="xaDataSourceClassName" value="com.mysql.jdbc.jdbc2.optional.MysqlXADataSource"/>
      <property name="xaProperties">
          <props>
              <prop key="url">jdbc:mysql://10.211.107.202:3306/hap_dev</prop>
              <prop key="user">hap_dev</prop>
              <prop key="password">hap_dev</prop>
          </props>
      </property>
  </bean>

  <!-- 其他sqlSessionFactory，mapperScannerConfigurer配置跟上面一样 -->
  <!-- 分布式事务 -->
    <bean id="atomikosTransactionManager" class="com.atomikos.icatch.jta.UserTransactionManager" init-method="init"
          destroy-method="close">
        <property name="forceShutdown" value="true"/>
    </bean>

    <bean id="atomikosUserTransaction" class="com.atomikos.icatch.jta.UserTransactionImp">
        <property name="transactionTimeout" value="300"/>
    </bean>

    <bean id="transactionManager" class="org.springframework.transaction.jta.JtaTransactionManager">
        <property name="transactionManager" ref="atomikosTransactionManager"/>
        <property name="userTransaction" ref="atomikosUserTransaction"/>
    </bean>
    <tx:annotation-driven/>
```

注意：  
 以上配置导致mybatis拦截器有一个问题，HAP3.3.0-RELEASE规避了这个问题，使用以上配置需要升级至HAP3.3.0-RELEASE及以上版本。

因为使用分布式事务管理，Transactional注解不再需要指定value，所有数据源的事务都讲统一管理，上述例子两个插入操作都会回滚。

> 使用多数据源配置，因为需要修改mapper的扫描路径，所以需要覆盖HAP的spring/applicationContext.xml 修改默认数据源dataSource的配置，其他数据源可以写在项目上的其他xml文件中

# 动态多数源配置

* 每次请求，使用同一个数据源，不存在上述事务问题。
* 无需配置多个sqlSessionFactory、mapperScannerConfigurer、transactionManager。
* applicationContext.xml中配置AbstractRoutingDataSource， 指定数据源集合targetDataSources与默认数据源defaultTargetDataSource。

```xml
    <!-- 数据源 -->
    <bean id="hap_dev" class="org.springframework.jndi.JndiObjectFactoryBean">
        <property name="jndiName" value="${db.jndiName}"/>
    </bean>
    <!-- 数据源 -->
    <bean id="hap_dev_1" class="org.springframework.jndi.JndiObjectFactoryBean">
        <property name="jndiName" value="${db1.jndiName}"/>
    </bean>

    <!-- 动态数据源配置-->
    <bean id="dataSource" class="org.springframework.jdbc.datasource.lookup.AbstractRoutingDataSource">
        <!-- 默认数据源 -->
        <property name="defaultTargetDataSource" ref="hap_dev"/>
        <!-- 数据源集合 -->
        <property name="targetDataSources">
            <map>
                <entry key="hap_dev" value-ref="hap_dev"/>
                <entry key="hap_dev_1" value-ref="hap_dev_1"/>
            </map>
        </property>
    </bean>
```

> 若是通过特定策略动态使用数据源，可继承AbstractRoutingDataSource重写方法determineCurrentLookupKey\(\)。determineCurrentLookupKey\(\)方法返回数据源名称，若返回值为null或返回值对应的数据源不存在，则使用defaultTargetDataSource数据源。详情可参考[动态数据源切换](https://www.cnblogs.com/daxin/p/3381793.html)。



