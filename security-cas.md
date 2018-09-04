# CAS集成

CAS 默认情况下是不启用的，启用CAS SSO的步骤如下：
## 1 修改 applicationContext-security.xml
在新项目的src/main/resources/spring目录下新增文件applicationContext-security.xml，文件内容如下：
```xml
<beans:beans xmlns="http://www.springframework.org/schema/security"
             xmlns:beans="http://www.springframework.org/schema/beans"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.1.xsd
                                 http://www.springframework.org/schema/security http://www.springframework.org/schema/security/spring-security-4.0.xsd">
    <beans:import resource="oauth2Security.xml"/>
    <!-- 根据项目需求选择使用CAS或标准登录方式  -->
    <beans:import resource="casSecurity.xml"/>
    <!--<beans:import resource="standardSecurity.xml"/>-->
 </beans:beans>
```

## 2 修改 config.properties
修改 src/main/resources/config.properties 文件，将其中的CAS部分根据实际情况修改：
```properties
#CAS
#本系统地址，后面的/login/cas必须有
cas.service=http://localhost:8080/hap/login/cas
#cas服务器登录地址
cas.ssoserver.loginurl=https://login.hand-china.com/sso/login
#sso服务器地址
cas.ssoserver.url=https://login.hand-china.com/sso
# SSO服务器退出地址，后面的http://localhost:8080/hap，根据据实际情况修改
cas.ssoserver.logouturl=https://login.hand-china.com/sso/logout?service=http://localhost:8080/hap
```
> 注意，以上内容中：端口 `8080` ，路径 `hap`  需要按实际情况修改

## 3 生成证书
接下来要导入CAS服务器的https密钥，以login.hand-china.com为例
  * 在core子项目下执行
  
```bash
  mvn exec:exec -Dexec.executable="java"  -Dexec.args="-classpath  ${basedir}/target/core/WEB-INF/classes com.hand.hap.security.InstallCert login.hand-china.com:443 "
```
  * 如果是HAP源码项目，在hap子项目下执行
```bash
mvn exec:java -Dexec.mainClass="com.hand.hap.security.InstallCert" -Dexec.args="login.hand-china.com:443"
```
  * 接下来提取密钥

```bash
keytool -exportcert -alias [host修改如hand-china.com]-1 -keystore jssecacerts -storepass changeit -file [host修改如hand-china.com].cer
```
  > 示例
  >> keytool -exportcert -alias login.hand-china.com-1 -keystore jssecacerts -storepass changeit -file hand-china.com.cer
 
  * 导入密钥

```bash 
keytool -importcert -alias [host修改如hand-china.com] -keystore [path to system keystore] -storepass changeit -file [host修改如hand-china.com].cer
```
  > 示例
  >> keytool -importcert -alias hand-china.com -keystore cas_xx -storepass changeit -file hand-china.com.cer
 
  *  将 jssecacerts 拷贝 到 $JAVA_HOME/jre/lib/security
  *  完成后删除生成的几个文件

## 4 准备用户
在登录之前要确认用户名在数据库中存在，临时的办法是先将admin改成SSO中使用的用户名。
  
  > 建议在配置 CAS 之前，用 admin 登录系统，新建一个用户，用户名为你的工号，并分配合适的角色
  
完成上面4个步骤后，重新编译项目，可以进行测试。


