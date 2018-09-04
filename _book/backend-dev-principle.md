1 编程规范
---
### 1.1 基本规范
1. 当面对不可知的调用者时，方法需要对输入参数进行校验，如不符合抛出`IllegalArgumentException`，建议使用 Spring 的 Assert 系列函数。
2. 隐藏工具类的构造器，确保只有static方法和变量的类不能被构造实例。
3. 变量，参数和返回值定义尽量基于接口而不是具体实现类   
如`Map map = new HashMap();`
4. 代码中不能使用~~System.out.println()，e.printStackTrace()~~，必须使用`logger`打印信息。

### 1.2 异常处理

1. 重新抛出的异常必须保留原来的异常，即`throw new NewException("message", e)`   
而不能写成~~throw new NewException("message")~~。
2. 在所有异常被捕获且没有重新抛出的地方必须写日志。 
3. 如果属于正常异常的空异常处理块必须注释说明原因，否则不允许空的catch块。
4. 框架尽量捕获低级异常，并封装成高级异常重新抛出，隐藏低级异常的细节。


### 1.3 JDK8
HAP 开发以及运行环境统一采用 java 1.8（或以上）版本。

项目开发人员可使用 java8所提供的特性来是代码更简练。如`lambda` 表达式等，同时，也要充分利用 java 高版本所带来的便利，如 `Annotation` 等。   

不需要关心的warning信息用`@SuppressWarnings("unused")`, `@SuppressWarnings("unchecked")`, `@SuppressWarnings("serial")` 注释。


2 业务逻辑开发规范
---
### 2.1 package 划分及命名

### 2.2 class命名
#### 2.2.1 Service

* Service接口： I(service 名称)Service
* Service实现：(service 名称) ServiceImpl

所有 Service 必须分开写接口类和实现类,切符合以上命名规则。

#### 2.2.2 DTO
BaseDTO类，包含了常规 DTO 所共有的基础字段，如标准 WHO 字段等，这些字段不需要重复编写在每一个 DTO 类中。如果一个 DTO 需要这些字段。则应该继承 BaseDTO 类，在使用该 DTO 进行 DML 操作时，标准字段由系统自动处理。  

常规DTO类：与数据库表相对应，例如 Order：

数据库中有表名为 sales_order_headers

则对应的基表DTO类为：SalesOrderHeaders.java，字段包含所有数据库表字段。

基表 DTO 类应继承 BaseDTO 类。

除此之外，还可以根据需要编写若干派生 DTO，仅包含指定的字段，也可以增加额外字段。派生 DTO 类，类名需加 For<用途>为后缀，如：   

SalesOrderHeadersForDelivery.java

#### 2.2.3 Mapper
基于基表的Mapper接口类，以及相对应的XML文件，应当与数据库表名有明确的对应关系，以Mapper作为统一后缀。   

当数据库中有表名为sales_order_headers时，相应的文件应为：
SalesOrderHeadersMapper.java   
SalesOrderHeadersMapper.xml    

使用 MyBatisGenerator 或其他工具自动生成以上文件时，应当保证目录结构和命名规范符合上述描述。  

#### 2.2.4 Controller

Controller 类统一以 Controller 作为后缀。
如无例外，Controller应该统一继承 BaseController 类，该类可以统一完成异常处理等公共逻辑。
Controller中的路径映射方法名称虽然没有实际用途，但名称应该有意义，切符合此文档中描述的命名规范。
在路径名称方面，应当符合项目的约定规范。并且可以区分数据请求还是页面请求。
此要求不作为统一的规定，但必须有，比如：

* @RequestMapping(value="/orderDetail.html")   
通过.html来知道是页面请求。
* @RequestMapping(value = "/orderDetail.json")    
通过.json来知道是数据请求。

Controller可以自动完成 URL 绑定，参数解析、转换、传递等操作。参数校验等。参数校验的细节参见5.4章节。

Controller主要用简单的逻辑来调用一个或若干个 Service 来完成一个复杂的逻辑，它本身不应该包含复杂的逻辑代码。

### 2.3 异常处理
#### 2.3.1 异常分类
异常总体上分为两种：
* 业务层异常
  * 对最终用户具有特定含义，如库存余额不足、订单已被冻结
  * 正常业务处理流程的一部分
  * 可预期
* 非业务层异常
  * 对最终用户没有意义，如IOException，NullPointerException
  * 由于程序Bug、运行时期异常、系统内部故障引起
  * 不可预期
#### 2.3.2 异常设计及命名规范
所有业务层异常应派生于统一基类`BaseException`，带有code及多语言描述。

BaseException 为预定义的业务类异常的基类，含有一个构造方法，该构造方法有3个参数：
* String code   
代表异常的 code。
* String descriptionKey   
代表异常的多语言描述 code。sys_prompts表中定义的多语言描述的 code。
* Object[] arguments   
descriptionKey 的消息中需要参数（如果没有参数传 null）。

自定义异常类应遵循以下规范：
1. 类名需要以 Exception 结尾
2. 异常需要存放在子模块的 package 内部的exception 包内，如：   `com.hand.hap.account.exception`。
3. 每个异常类都应该有详细的 javadoc ，说明该异常在什么情况下发生。


自定义业务异常类需要符合以下规范：
1. 每一类业务异常都有一个唯一的 code    
日志需要记录，调试时也依据异常 code 来知道到底发生了什么问题。code 的命名需要符合以下规范：  
* 采用`大写字母`，`_`，`数字`组成
* 以模块代码开头，整体命名规范参见常量命名    
如INV_QTY_INSUFFICIENT   
2. 错误描述的 descriptionKey 可以有多个，至少有一个。
每个 descriptionKey 都应该有多个语言的版本（系统支持所有语言环境都要有）   
descriptionKey用于自动异常处理，将对应语言的消息文本反馈给最终用户    
descriptionKey 的定义是在 HAP 的一个系统功能 “系统描述”内录入，命名需要符合此功能的规范。
3. 自定义异常不需要在构造时再要求传入 code 参数，而应该在内部定义。
4. 自定义异常应该提供多个构造方法，以满足不同场景的需求，例如：   
  * 无参构造。（descriptionKey 默认）。
  * 仅需要descriptionKey（无参数）
  * 需要descriptionKey，arguments。


#### 2.3.3 异常使用规范
一般情况下，业务类异常都是由 Service 层抛出，在 Controller 层处理。

BaseException为Exception的子类（不是RuntimeException），在使用时需要声明抛出，因此 Service 中大部分方法都需要声明抛出BaseException或者其子类异常。

一般规则如下：
* 非查询类方法需要声明抛出 BaseException。
* 查询类方法如果没有必要，不需要声明。
* Service层应该只声明抛出业务类异常，所有其他异常统一转换为RuntimeException抛出。
* Controller 在调用 Service时，如果有 BaseException 需要抛出，如果没有特殊情况，应当继续声明抛出。
Controller 层在基类有公共代码负责捕获异常，并将异常转换（翻译）为可读懂的消息，继而展示给最终用户。


> 注意：Controller需要继承框架提供的统一基类 BaseController 才能具有自动处理异常的能力。详见2.2.4章节。

### 2.4 参数校验
Controller 层可以调用系统 API 完成简单的参数校验，更复杂的业务逻辑校验请使用业务类异常处理，参见2.3章节。   

简单参数校验主要围绕 DTO 类展开，例如要求某个字段必须输入值，或者输入的值必须满足一个简单的条件等。

#### 2.4.1 基于Annotation的校验
HAP 主要采用基于 Annotation 的注解说明。只需要在相应的 DTO 的某个字段上加上 Annotation，框架会根据 Annotation 的含义来对字段值进行校验。
这些常用的 Annotation 包括：
* @NotNull
* @Email
* @Length(min=6, max = 12)
* @Range(min = 1, max = 12)
* @Min(value=4)
* @Max(value=10)
* @Null
* @NotNull
* @Digits
* @Pattern

#### 2.4.2 标准字段描述定义规范
开发人员不需要对 Annotation 附加额外的消息说明，具体的错误消息会有框架自动处理，但需要按照约定预定义消息字段多语言描述。   

描述的 code 须符合规范：   
`dto的简短类名` . `字段名` 全部小写。
例如： `user.username`   

并且需要在“系统描述”功能中定义多语言描述：
  * en：User Name
  * zh_CN：用户名
  
> 注意，此描述信息不仅仅是在自动校验中使用，在界面显示中也会用到，比如 Grid 组件的列的描述信息。   
因此每个 DTO 的需要暴露给用户的字段，都应该按照上述规范为其定义多语言描述。

### 2.5 日志使用规范
HAP 系统使用SLF4J作为日志输出接口，在底层可以根据需要使用 JDK 的日志实现或者 LOG4J等，可根据实际使用场景切换日志实现。默认采用 Logback作为日志实现。
关于日志的组织分类，各种场景配置等问题，可以在日志配置文件层面实现。详细内容可以参考框架详细设计，日志系统部分。

#### 2.5.1 获取 logger 实例
在实际代码编写中，不应该直接使用 Log4j的 Logger 来输出日志。而应该统一调用SLF4J的 API方法。
```java
Logger logger = LoggerFactory.getLogger(XXX.class);
```
其中 Logger 和LoggerFactory 均来自Package org.slf4j。

#### 2.5.2 日志输出基本原则
在输出日志方面，应遵循以下原则：
1. 准确区分应该使用的日志级别。系统的日志输出级别会根据需要动态调整。非关键信息应该采用低级别输出，避免输出太多无用信息。关键信息应该高级别输出，确保不会遗漏。建议的级别如下：
 * info级别，输出普通方法执行细节，接收的参数，返回的数据。
 * warn级别，输出潜在的问题，但不包括致命错误。例如缺少指定参数，使用了默认参数。
 * debug级别，输出系统、复杂模块初始化的步骤细节等，关键代码执行明细，也可以输出与业务相关的逻辑执行明细，相关数据等。 debug 级别日志是排查错误的重要依据，应尽可能输出有效的信息。
 * error级别，只用于输出系统发生的错误。 

2. 记录异常时，不应该只记录 message，还应该记录堆栈详细内容，需要调用合适的方法，传入异常。
3. 不能在代码中使用 System.out.println() 输出内容替代日志的功能。
4. 避免无意义的输出，避免遗漏必要的信息。输出日志应考虑实际情况，输出一些有助于帮助系统排查错误，或者分析系统行为的内容。
5. 禁止在日志中输出`敏感信息`，如密码，信用卡信息等。

### 2.5.3 常规业务逻辑记录日志
常规业务逻辑通常在Service中完成，Service中的方法将由框架自动拦截，并记录执行概要。但具体的背部细节还需要开发者手动编码指定输出内容。
目前自动完成的内容有：
* Service方法接收到的参数
* Service方法的返回值
* Service方法执行时间

这些内容不需要开发者再手动输出，以免重复。

当在 Service 中去调用其他不能被框架拦截的方法时，如果有必要，可以手动记录类似的执行概要。
具体到方法内部的细节，可以自行划分步骤、关键点等，在这样的临界位置输出必要的信息，可以采用INFO或 DEBUG 级别输出。
对于循环操作，需要特别注意，避免输出大量的日志内容。


#### 2.5.4 异常记录
框架有自动异常处理功能，可能以自动、统一的记录日志，保证不会重复、遗漏。
当手动 try-catch捕获到异常时，有以下几种情况：

* 吞没该异常。视情况记录日志。
* 转换类型后继续抛出异常（或直接抛出）。此时可以只记录错误消息，而不必记录错误堆栈，详细错误将由后续异常捕获者处理。如果后续代码已经脱离框架控制范围，则应当记录堆栈明细。
* 处理异常，并继续抛出一个新异常（空堆栈）。视情况记录原始异常堆栈。

应当避免将一个异常在不同的地方重复记录。
> 并非所有异常都需要记录详细的堆栈信息，当一个异常将处理或被忽略时，需要具体场景具体分析，但千万要小心，不能将重要信息遗漏！   

### 2.6 事务控制
#### 2.6.1 事务声明
HAP 采用 Spring 声明式的事务管理。原则上不应该在代码的任何地方出现 commit 和 rollback 操作，而应在现行框架内自动完成。

使用`@Transactional`注解来为指定的方法指明事务类型。关于 Spring事务类型的详细内容，请参考 Spring官方文档。

在一般情况下，遵循以下原则：
* 仅在Service 层做事务控制
  * Service 提供给各种 API 调用，可以保证各种方式调用 Service 都能达到同样的事务效果。Controller仅仅是做 URL 映射
  * 需要调用多个方法，又需要同一个事务的操作，需要在 Service 中提供聚合方法来完成操作。
* 查询类方法的事务传播级别默认为：SUPPORTS。
* 确定不需要事务支持的方法，事务传播级别为：NOT_SUPPORTED
* 其他需要事务支持的方法，传播级别为默认：REQUIRED