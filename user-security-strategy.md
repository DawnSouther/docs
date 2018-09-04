# 用户安全策略

框架提供标准的用户安全策略接口

com.hand.hap.security.IUserSecurityStrategy

> 默认实现DefaultUserSecurityStrategy，order值为9999

编写自定义实现类，需要在 Spring 中定义为 bean （也可以通过 @Component之类的注解自动扫描注册）

可以实现下列方法，自定义安全策略：

### 登录成功以后，跳转至index页面前的策略

```java
ModelAndView loginVerifyStrategy(User user, HttpServletRequest request);

```


参数及返回类型说明

>  - user 通过登录验证的User对象
- ModelAndView 重定向到该 ModelAndView 
>
 如果想要正常跳转到index页面，请返回null

注意：会根据order大小依次执行实现类的该方法，注意顺序





### 用户修改密码时，自定义密码复杂度
 
```java

  void passwordVerifyStrategy(IRequest request ,User user,String oldPwd, String newPwd, String newPwdAgain) throws UserException;

```

参数及异常说明



> - user 通过登录验证的User对象
- oldPwd 旧密码
- newPwd 新密码
- newPwdAgain 再次输入的新密码
- 如果验证不通过，请抛出自定义的UserException

一般来说修改了复杂度验证策略，需要修改sys_config页面。

###  新建用户时的策略

 

```java

User beforeCreateUser(IRequest request ,User user)

```

可以对用户做统一的信息处理，如统一设置一些属性值，注意如果实现了该方法，一定要返回处理后的User对象，默认返回传入的user


> 实现IAuthenticationSuccessListener
 接口， 是在用户登录验证成功时执行.
>
一般用来往session里面放一些值，或其他逻辑，如果抛出异常，则会在login页面显示异常信息，注意区别。









