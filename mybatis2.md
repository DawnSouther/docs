
# 开发手册

## 1.MyBatis开发注意事项**

### 自动化 CRUD
Mapper 接口继承 通用 Mapper< T >，可以自动完成基本的增删改查操作

```java
public interface RoleMapper extends Mapper<Role> {
}
```

> 动态 SQL

MyBatis 可以用 一些内置的标签来动态的组织 SQL 语句，常用的标签有：

* if
当 test 语句通过时，if 内包含的 SQL 会起作用

```xml
<if test="userId != null">
user_id = #{userId,jdbcType=INTEGER}
</if>
```
* where
以 where 开头，将一组标签的返回值拼接起来，自动去除首尾多余的`AND` `OR`
专门用于生成动态 WHERE 条件

```xml
<where>
<if test="codeId != null">
code_id = #{codeId,jdbcType=INTEGER}
</if>
<if test="code != null">
AND code = #{code,jdbcType=VARCHAR}
</if>
</where>
```
* set
以 set 开头，将一组标签的返回值拼接起来，自动去除首尾多余的逗号 `,`
专门用于生成动态的 set 语句

```xml
<set>
<if test="userName != null">
user_name = #{userName,jdbcType=VARCHAR},
</if>
<if test="userAge != null">
user_age = #{userAge,jdbcType=INTEGER},
</if>
</set>
```
* trim
功能强大的标签，包含 where 和 set 的功能
将一组标签的结果拼接起来，支持指定移除首部或尾部指定的内容，支持自动在首部或者尾部添加指定的内容

```xml
<trim suffix="WHERE" suffixOverrides="AND | OR">  
<if test="id != null and id !='' ">  
        AND b.id =#{id}   
</if>  
<if test="name != null">  
        AND b.menu_name like #{name}  
    </if>  
</trim>
```
where 的 trim 写法

```xml
<trim prefix="WHERE" suffixOverrides="AND | OR">
<if test="codeId != null">
code_id = #{codeId,jdbcType=INTEGER}
</if>
<if test="code != null">
AND code = #{code,jdbcType=VARCHAR}
</if>
<trim>
```
set 的 trim 写法

```xml
<trim prefix="SET" suffixOverrides=",">
<if test="userName != null">
user_name = #{userName,jdbcType=VARCHAR},
</if>
<if test="userAge != null">
user_age = #{userAge,jdbcType=INTEGER},
</if>
</set>
```
* foreach
用于遍历访问集合内的元素

```xml
<foreach collection="_parameter" item="item" open="(" separator="," close=")">
#{item}
</set>
```

### 2.Spring

#### 2.1DTO定义

#### 2.1.1继承关系
> BaseDTO类 包含了常规 DTO 类的所有公共特性

* _token 防篡改验证
* 标准 WHO 字段
* 扩展属性字段


如果没有特殊情况，当新建一个 DTO 类时，应当继承 BaseDTO 类，不要把重复的内容写到每一个类中。

``` java
public class XXX extends BaseDTO {
...
}
```

除了避免代码重复以外，BaseDTO 类在系统的各种地方都发挥着重要的作用

* 插入数据时自动完成 WHO 字段的赋值
* 数据多语言 **增删改**（没有查）时，自动对相应的 TL 表操作
* 防篡改 TOKEN 验证

> 数据多语言

当一个 DTO 需要支持数据多语言时，需要通过 Annotation 进行注解说明

```java
@MultiLanguage
@Table(name = "sys_role_b")
public class Role extends BaseDTO {
    @Id
    private Integer roleId;

    @MultiLanguageField
    private String roleName;

    @MultiLanguageField
    private String roleDescription;
}
```

* `@MultiLanguage`
用于说明改 DTO 类需要支持数据多语言
* `@Table`
用于说明改 DTO 对应的数据库中的表的名字
* `@Id`
用于说明该表中的主键，如果是联合主键，需要都加上`@Id`
* `@Column`(可选)
用于说明该字段在数据库中的列名
* `@MultiLanguageField`
用于说明该字段为多语言字段

数据多语言的增删改由拦截器自动完成

#### 2.1.2头行
> @Children 注解

这个注解在头行自动化处理中起到十分关键的作用

```java
public class Code extends BaseDTO {
...
@Children
private List<CodeValue> codeValues;
...
}
```
* 自动属性蔓延
前面说了 BaseDTO 的`标准 who`属性，当`系统`对`头`设置这些属性时，会自动蔓延至@Children 标注的属性。
这些属性的类型可以是 List< BaseDTO>类型，也可以是一个普通 BaseDTO 对象。
* 自动校验行数据
当调用 validator 进行数据校验时，行数据也会自动进行校验，不需要手动循环校验。

> 使用 validator

在 Controller中使用 validator 进行自动的校验

```java
@RequestMapping(value = "/sys/role/submit", method = RequestMethod.POST)
    @ResponseBody
    public ResponseData submitRole(@RequestBody List<Role> roles, BindingResult result, HttpServletRequest request)
            throws BaseException {
        getValidator().validate(roles, result);
        if (result.hasErrors()) {
            ResponseData rd = new ResponseData(false);
            rd.setMessage(getErrorMessage(result, request));
            return rd;
        }
        return new ResponseData(roleService.batchUpdate(roles));
    }
```

需要按照规范声明参数，然后进需要调用一行语句既可。
自动检验可以判断需要检验对象的类型，List或者单个 DTO 对象都支持。并可以可以递归的校验对象中使用`@Children`标注的子属性。

#### 2.1.3校验注解

```java
public class Code extends BaseDTO {
@NotEmpty
private String code;
...
}
```

通过使用注解`@NotEmpty`可以声明一个约束校验，在校验的过程中，系统自动判定属性值是个符合注解的含义。校验失败时错误能被框架翻译为友好多语言的提示信息。

类似的注解还有很多
@NotNull
@Email
@Length(min=6, max = 12)
@Range(min = 1, max = 12)
@Min(value=4)
@Max(value=10)
@Null
@NotNull
@Digits
@Pattern

使用这些注解可以完成一些简单的校验，并且不需要写过多的繁琐的内容。

需要注意的是：
需要为数据库中的所有表的列定义 prompts 信息

例如 DTO: `com.hand.hap.account.dto.Role`，有一些属性，以 roleCode 为例。
需要定义多语言 prompt：
role.rolecode

*  Role Code
en_GB
* 角色代码
zh_CN

用的到的字段都需要定义，界面显示也会用的到，自动校验提示消息也会用的到。


### 2.2 Service层

#### 2.2.1 基本原则
非查询类接口需要声明抛出 BaseException 的子类，**不推荐**直接抛出 BaseException。

> IRequest 参数

IRequest 参数为 Service 请求的标准参数，每一个在 Controller 中调用的 service 方法都需要这个参数。他用来获取 `locale`，`userId`，`roleId` 等 session 中的信息。也可以传递额外的属性。

> 查询

标准的参数格式，以及数据返回

```java
@Transactional(propagation = Propagation.SUPPORTS)
public List<Code> selectCodes(IRequest requestContext, Code code, int page, int pagesize) {
    PageHelper.startPage(page, pagesize);
    List<Code> codes = codeMapper.selectCodes(code);
    return codes;
}
```
* 声明 SUPPORTS 级别的事务
* 接收 page，pagesize的分页参数
* 标准分页代码
* 返回类型 List

这仅是一个常用范例，至于分页和返回类型还是要看具体的需求。

> 提交

```java
public List<Role> batchUpdate(IRequest requestContext, List<Role> roles) {
    for (Role role : roles) {
        if (role.getRoleId() == null) {
            self().createRole(role);
        } else {
            self().updateRole(role);
        }
    }
    return roles;
}
```
* 支持批量更新，自动判断
* 返回 List（分布式调用 Service 时，如果不返回数据，会导致插入时生成的主键调用方取不到）

对于单条记录的更新，插入操作，也需要返回接收到的参数。

> 删除

```java
public void batchDelete(IRequest requestContext, List<Role> roles) {
    for (Role role : roles) {
        if (role.getRoleId() != null)
            self().deleteRole(role);
    }
}
```

* 支持批量删除
* 一般不需要返回数据

删除单条记录时，如果需要自动删除相应的数据多语言，则**需要用 DTO 作为参数，不能仅仅使用主键**。

> Service 自调用

当出现一个 service 调用自己的另外一个方法（在接口中声明过）时，需要按照约定，使用 `self().`作为调用引用。

```java
public void method1(IRequest request) {
  self().method2(request);
}
```

### 2.2.2事务控制
#### 事务声明
HAP采用 Spring 声明式的事务管理。原则上不应该在代码的任何地方出现 commit 和 rollback 操作，而应在现行框架内自动完成。

使用`@Transactional`注解来为指定的方法指明事务类型。关于 Spring事务类型的详细内容，请参考 Spring官方文档。
在一般情况下，遵循以下原则：
* 仅在Service 层做事务控制
* Service 提供给各种 API 调用，可以保证各种方式调用 Service 都能达到同样的事务效果。Controller仅仅是做 URL 映射
* 需要调用多个方法，又需要同一个事务的操作，需要在 Service 中提供聚合方法来完成操作。
* 查询类方法的事务传播级别默认为：`SUPPORTS`。
* 确定不需要事务支持的方法，事务传播级别为：`NOT_SUPPORTED`
* 其他需要事务支持的方法，传播级别为默认：`REQUIRED`





### 2.3Controller层

如无例外，一个普通的 Controller 需要继承 BaseController

```java
@Controller
public class RoleController extends BaseController {
...
}
```

BaseController 提供一些通用的用途

* initBinder
* exceptionHandler
* 获取校验产生的错误信息
* validator
* messageSource
* 默认分页参数值
* createRequestContext(HttpServletRequest) 方法
* 校验 _token

> 查询

标准查询写法

```java
@RequestMapping(value = "/sys/role/query")
@ResponseBody
public ResponseData getRoles(Role example,
                             HttpServletRequest request,
                             @RequestParam(defaultValue = DEFAULT_PAGE) int page,
                             @RequestParam(defaultValue = DEFAULT_PAGE_SIZE) int pagesize) {
    IRequest requestContext = createRequestContext(request);
    return new ResponseData(roleService.selectRoles(requestContext, example, page, pagesize));
}
```
* IRequest 参数的创建
* 默认分页参数支持
* 返回 ResponseData
* 使用返回的 List 数据直接构造 ResponseData
ResponseData用于Grid 的数据,有固定结构:

```json
{
"success": true,
"total": 20,
"rows": [
{"name":"aa"},
{"name":"bb"}
]
}
```

> 提交数据

```java
@RequestMapping(value = "/sys/role/submit", method = RequestMethod.POST)
@ResponseBody
public ResponseData submitRole(@RequestBody List<Role> roles, BindingResult result, HttpServletRequest request)
        throws BaseException {
    getValidator().validate(roles, result);
    if (result.hasErrors()) {
        ResponseData rd = new ResponseData(false);
        rd.setMessage(getErrorMessage(result, request));
        return rd;
    }
    IRequest requestContext = createRequestContext(request);
    return new ResponseData(roleService.batchUpdate(requestContext, roles));
}
```
* @ResponseBody 声明的 List 传参
* List 参数后面紧跟的 BindingResult 参数
* request 用于获取Locale（取多语言错误消息）
* 调用 validator 对数据进行校验
* IRequest 参数
* 有错误时，返回包含错误的 ResponseData
* 正常返回包含数据的 ResponseData


### 2.4异常记录

框架有自动异常处理功能，可能以自动、统一的记录日志，保证不会重复、遗漏。

当手动 try-catch捕获到异常时，有以下几种情况：
* 吞没该异常。视情况记录日志。
* 转换类型后继续抛出异常（或直接抛出）。此时可以只记录错误消息，而不必记录错误堆栈，详细错误将由后续异常捕获者处理。如果后续代码已经脱离框架控制范围，则应当记录堆栈明细。
* 处理异常，并继续抛出一个新异常（空堆栈）。视情况记录原始异常堆栈。

> **应当避免将一个异常在不同的地方重复记录**。

并非所有异常都需要记录详细的堆栈信息，当一个异常将处理或被忽略时，需要具体场景具体分析，但千万要小心，不能将重要信息遗漏！



### 2.5日志使用规范

HAP使用SLF4J作为日志输出接口，在底层可以根据需要使用 JDK 的日志实现或者 LOG4J等，可根据实际使用场景切换日志实现。默认采用 Logback作为日志实现。

关于日志的组织分类，各种场景配置等问题，可以在日志配置文件层面实现。详细内容可以参考框架详细设计，日志系统部分。

#### 2.5.1获取 logger 实例

在实际代码编写中，不应该直接使用 Log4j的 Logger 来输出日志。而应该统一调用SLF4J的 API方法。

```java
Logger logger = LoggerFactory.getLogger(XXX.class);
```
其中 Logger 和LoggerFactory 均来自Package `org.slf4j`。

#### 2.5.2日志输出基本原则

在输出日志方面，应遵循以下原则：
1. 准确区分应该使用的日志级别。系统的日志输出级别会根据需要动态调整。非关键信息应该采用低级别输出，避免输出太多无用信息。关键信息应该高级别输出，确保不会遗漏。建议的级别如下：
 1. info级别，输出普通方法执行细节，接收的参数，返回的数据。
 2. warn级别，输出潜在的问题，但不包括致命错误。例如缺少指定参数，使用了默认参数。
 3. debug级别，输出系统、复杂模块初始化的步骤细节等，关键代码执行明细，也可以输出与业务相关的逻辑执行明细，相关数据等。 debug 级别日志是排查错误的重要依据，应尽可能输出有效的信息。
 4. error级别，只用于输出系统发生的错误。
2. 记录异常时，不应该只记录 message，还应该记录堆栈详细内容，需要调用合适的方法，传入异常。
3. 不能在代码中使用 `System.out.println()` 输出内容替代日志的功能。
4. 避免无意义的输出，避免遗漏必要的信息。输出日志应考虑实际情况，输出一些有助于帮助系统排查错误，或者分析系统行为的内容。
5. 禁止在日志中输出敏感信息，如密码，信用卡信息等。

#### 2.5.3常规业务逻辑记录日志

常规业务逻辑通常在Service中完成，Service中的方法将由框架自动拦截，并记录执行概要。但具体的背部细节还需要开发者手动编码指定输出内容。
目前自动完成的内容有：
* Service方法接收到的参数
* Service方法的返回值
* Service方法执行时间
这些内容不需要开发者再手动输出，以免重复。

当在 Service 中去调用其他不能被框架拦截的方法时，如果有必要，可以手动记录类似的执行概要。

具体到方法内部的细节，可以自行划分步骤、关键点等，在这样的临界位置输出必要的信息，可以采用`INFO`或 `DEBUG` 级别输出。

对于循环操作，需要特别注意，避免输出大量的日志内容。
