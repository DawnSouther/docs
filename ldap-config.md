# 开启LDAP验证



HAP框架支持LDAP验证（如果还没有LDAP服务器,可以参考[LDAP服务器搭建](ldap-server.md)) 启用LDAP验证的步骤如下：

## 1 修改 standardSecurity.xml

修改src/main/resources/spring目录下的文件standardSecurity.xml，文件54行左右内容如下：

```xml
<!--<beans:import resource="standardSecurity-LDAP.xml"/>-->
<authentication-manager>
 <!--Ldap验证-->
 <!-- <authentication-provider ref="ldapAuthProvider" />-->
 <!--标准登录验证-->
  <authentication-provider user-service-ref="customUserDetailsService">
  <password-encoder ref="passwordManager"/>
  </authentication-provider>
</authentication-manager>


```

可以同时开启Ldap验证和标准验证，注意两个provider顺序，优先验证前者。

>开启ldap验证 请把<beans:import>...也取消注释，注册相关bean

>如果同时使用两种验证，注意密码问题，hap中设置的密码和ldap中的密码将都可以成功登录。建议做法是，导入到数据库里的用户，密码都设置为null或者空字符串，则用户密码验证不会通过



## 2 修改 config.properties

修改 src/main/resources/config.properties 文件，将其中的LDAP部分根据实际情况修改：

```properties

#LDAP
#LDAP服务器的URL 可以设置远程服务器　注意是ldap开头的url
ldap.server.url=ldap://localhost:389/
#登录LDAP服务器的用户凭证 根据实际服务器的配置修改
ldap.conn.userDn=cn=Manager,dc=hand,dc=com
ldap.conn.password=secret
#用户查询策略 在指定的目录下查询
ldap.user.search.base=dc=hand,dc=com
#匹配条件 默认匹配uid　
ldap.user.search.filter=(uid={0})

```

至此LDAP验证已经开启，确保LDAP服务器正常运行，且用户表中有与LDAP指定目录相同的用户名，并且分配适当角色，则可以正常登陆

如果需要更精确的配置请参考文章
  http://lengyun3566.iteye.com/blog/1358321

  修改 src/main/resources/spring目录下的配置文件 standardSecurity-LDAP.xml







