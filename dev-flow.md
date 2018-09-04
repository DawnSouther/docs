# 开发流程

## 后端开发

### 业务需求整理


### 设计表结构


### 编写 liquibase 脚本

先列一个参考资料 https://github.com/tlberglund/groovy-liquibase/blob/master/src/test/changelog/changelog.groovy

### 编写 DTO

#### 创建 DTO 类

 * DTO 类不需要提供任何实现，所以属于供应方的服务接口层。创建在 `项目模块` 的 `xxx.<module>.dto ` 包下。
 * 每一个 DTO 类即为一个实体类，对应数据库中的一个具体表。
 * 名称与表名称相同，表名中 ` _ ` 替换为驼峰命名法，首字母大写，且忽略前缀。如：` UserRole ` 对应表为 ` sys_user_role `。
 
 > 关于字段命名    
 > HAP 依赖 `java bean` 命名规范.     
 > 请严格遵循规范定义`属性`,`getter`,`setter`等.


#### 指定对应表

 * ` @Table(name = "table_name") ` 指定 DTO 对应数据库中表的名称。
 * 每一个 DTO 对应数据库中的一个具体表，一般都需要继承 BaseDTO 类。

#### 属性规范

 * 所有属性均为`private`属性。
 * 每一个属性需要生成对应的 ` getter ` 和 ` setter ` 方法。
 * 字段名称应根据`驼峰命名规则`从数据库列名转换过来
   * 例如：数据库列名为 USER_NAME，则字段名为 userName
   * 特殊字段名称，可以在字段在添加`@Column(name = "xxx")` 注解，指定数据库列名
 * 非数据库字段，需要用`@Transient` 标注
   * javax.persistence.Transient
 * 属性的的类型与字段的 type 对应
   * 不使用基本类型，全部使用基本类型的包装类，如 ` Long ` 对应数据库中的 ` INTEGER `，而不是使用 ` long `
   * 数字类型主键统一采用 `Long`
   * 金额、数量 等精度严格浮点类型采用 `BigDecimal`
     * 注意 BigDecimal 在计算、比较方面的特殊性
 * 所有的主键字段都需要用`@Id`标注
   * 对于自增张、序列（SEQUENCE）类型的主键，需要添加注解`@GeneratedValue`
   * 序列命名规范：`表名_S`
     * 例如：表 SYS_USER 对应的序列为 SYS_USER_S

#### 数据多语言支持
> hap 数据多语言可以通过在 DTO 上添加注解来自动完成

  * DTO 类上添加`@MultiLanguage`
    * 此注解说明该 DTO 需要支持数据多语言
  * DTO 字段上添加 `@MultiLanguageField`
    * 此注解说明该字段是一个多语言字段
    
    * 当有多个多语言字段时，这些字段都需要添加

> 实际作用

* 当使用 DTO 执行标准 insert 操作时，框架会自动插入多条数据到对应的 TL 表
* 当执行 delete 操作时，会自动删除对应的多语言数据
* 当执行标准 query 时，会自动关联 TL 表


#### 参照

https://rdc.hand-china.com/gitlab/HAP/hap/raw/master/hap/src/main/java/com/hand/hap/account/dto/Role.java

### 编写 Mapper

#### 创建 Mapper 接口类

 * Mapper 接口类即为传统意义上的 DAO，但与 ` interface ` 不同，Mapper 本身就是对数据访问的具体实现，所以属于供应方的服务实现层。创建在 `项目模块` 的 `xxx.<module>.mapper` 包下。
 * 每一个 Mapper 接口类封装了对数据库表的操作，每一个 Mapper 对应一个 DTO 类，所以命名为 DTO 类名 + `Mapper`。如：` UserRoleMapper ` 对应表为 ` UserRole ` 类。
 * 基础的 CRUD 操作不需要再次实现，通过继承 ` Mapper<T> ` 类实现。其中 T 为 对应 DTO 的泛型。
 * 复杂的数据库操作需要定义具体的接口方法。

#### 创建 Mapper.xml

 * Mapper.xml 是数据库的的具体映射，与 Mapper 接口同级，创建在 `项目模块` resources 目录的 ` xxx.<module>.mapper ` 包下。
 * Mapper.xml，与 Mapper 接口对应。所以命名 Mapper 接口类相同。
 * 对于基本的 CRUD 不需要进行配置，所以也就不需要创建对应的 Mapper.xml 文件。
 * 对于自定义的数据库方法，需要创建对应的 Mapper.xml 文件。
 * Mapper.xml  中的操作 id 对应 Mapper 接口类的方法名。

#### 基础 CRUD 操作

| 操作 | 方法 |
| -------- | -------- |
| **插入** | ### |
| 插入一条 | ` int insertSelective( T entity ); ` |
| **删除** | ### |
| 根据 ID 删除一条 | ` int deleteByPrimaryKey( Object id ); ` |
| **修改** | ### |
| 根据 ID 修改 | ` int updateByPrimaryKeySelective( T entity ); ` |
| **查询** | ### |
| 根据 ID 查询 | ` T selectByPrimaryKey( Object id ); ` |
| 根据条件查询一条记录 | ` T selectOne( T entity ); ` |
| 根据条件，查询全部记录 | ` List<T> select(T example); ` |

### 编写Service

#### 创建 Service 接口类

 * Service 接口类定义了业务操作的一系列接口，并不提供实现，具体实现需要通过服务实现层提供，所以属于供应方的服务接口层。创建在 `项目模块` 的 `  xxx.<module>.service ` 包下。
 * 接口（interface）统一以大写字母 `I` 做为命名前缀
 * 每一个 Service 对应一个 DTO 类，所以命名为`I` + DTO 类名 + ` Service `。如：`  IUserRoleService ` 对应表为 ` UserRole ` 类。
 * Service 里的每一个方法需要加上IRequest对象作为参数。
 * Service 接口，如无特殊例外，需要继承 `ProxySelf< T >` 接口
   * T 为 Service 本身


#### 创建 Service 实现类

 * Service 接口的具体实现通过服务实现层提供，所以属于供应方的服务实现层。创建在 `项目模块` 的 ` xxx.<module>.service.impl ` 包下。
 * 每一个 Service 实现类对应一个 Service 接口类，所以命名为 Service 接口类名(去掉`I`前缀) + ` Impl `。如：` UserRoleServiceImpl ` 对应 ` IUserRoleService ` 类。
 * 实现类，如无特殊情况，需要用`@Service`标注，以自动扫描注册
 * 实现类可以通过继承`BaseServiceImpl< T >` 来获得标准的 CRUD 操作支持
   > 需要 Service 接口类 继承 `IBaseService< T >`
 * ServiceImpl 中对于 Mapper 的 CRUD 操作参照 [基础 CRUD 操作](#基础 CRUD 操作])。

### 编写Controller

#### 创建 Controller 类

 * Controller 负责对 Model 和 View 的处理，创建在 `项目模块` 的 ` xxx.<module>.controllers ` 包下。
 * 每一个 Controller 是对一个具体的 DTO 资源进行处理的，所以命名为 DTO 类名 + ` Controller `。如： ` UserRoleController  ` 对应 ` UserRole ` 类。
 * 需要通过 ` @Controller ` 指定该类为一个 Controller 类。
 * 需要在每一个 Controller 中通过 ` @Autowired ` 注入 Service。
 * Controller 的每一个方法只在最后调用一次该 Controller 所注入的 Service ，因此当有调用多个Service的需求应该放在注入的 Service 中。 


## 前端开发
### 界面设计
