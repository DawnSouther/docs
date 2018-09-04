本文描述 HAP 自 1.0 版发布(2016-06-15)以来 的变化概要汇总.
目前这些内容均包含在 HAP2.0 中.

新特性
---
1.  新增组织架构模块
    * 现在系统带有组织结构的功能,可以维护`公司`,`员工`,`部门`,`岗位`的基础数据
2.  自定义缓存 自动扫描
    * 对于实现了`Cache < T >` 接口的缓存实现,现在会被自动扫描注册,不需要再修改 xml 配置文件手动注册
3.  自定义 FreeMarker bean 的注解支持
    * 现在可以使用注解@FreeMarkerBean 来自定义一个会被自动加载的 bean,在 freemarker 标记中使用
4.  权限校验,现在支持参数化 url 匹配
    * 现在可以注册参数化的 url,比如: `sys/file/{fileId}/view`
5.  升级通用 mapper 的排序功能
    * 现在根据 BaseDTO 中的 `sortname`,`sortorder` 来动态生成排序的 sql
    * 同时会结合主键,来保证排序的唯一性
    
    > 自动生成的 sql,会检查合法性,阻止 sql 注入
6.  乐观锁机制
    * 通用mapper 的 更新类操作增加 where 条件 object_version_number (如果存在的话)
    * 通用 Service的基础方法,会对  更新类操作的返回值进行校验,判断是否操作失败
    * 新增 mybatis拦截器,会在 insert,update 成功以后,修改参数中的object_version_number 字段
7.  基础数据管理
    * 基础建表脚本,全部采用 liquibase 命令
    * 基础数据管理,基于 excel,可持续维护
8.  多角色合并
    * 现在登录系统时不再需要选择角色
    * 用户的看到的菜单,权限,由所有角色合并而来
    
    > IRequest 中仍然有一个默认的角色ID
9.  OAuth 在使用用户名密码获取凭证时,会直接使用系统中的用户
10.  优化计划任务
    * 当任务类名无效时,现在的错误提示更容易看懂
    * 现在AbstractJob 可以监听自己的执行状态
      *  执行之前:jobToBeExecuted
      *  执行之后:jobWasExecuted
      *  执行之后,可以获取到当前执行记录的 ID
11.  功能定义,采用全新标准的字体图标
    * 现在图标为标准的 fontawesome 图标 fa fa-xx
12.  新增系统配置功能
    * 验证码策略
    * 新用户密码
    * 系统 logo,title 等
    * 支持 API 级别的参数订阅
13.  主动加载通用描述
    * 现在会自动加载以 hap.开头的描述
14.  IRequest 增加 employeeCode 属性
    * 对应工作流需求
15.  工作流(详见下文)

16. [增加 WebService 发布支持](webservice-cxf.md)
 * 自动扫描 cxf-beans-*.xml 中定义的 service
 * 默认没有权限控制, 可修改 cxfSecurity.xml 自定义



bug 修复
---
1.  修复 _token 验证的 bug
    * 现在是根据主键是否有值来决定是否跳过校验
2.  修复静态资源文件不能被浏览器缓存的问题
3.  修复OAuth 与 CAS 一起时用时,无法使用 secret 获取 token 的问题
4.  修复验证码 cookie 问题
    * 部署 hap 在根路径时,ie 浏览器无法获取验证码 cookie
5.  修复功能分配导致403 的问题
    * 给功能分配新的资源时,删除了已分配的资源
6.  修复功能定义 缓存只影响当前语言环境的问题
7.  修复资源分配会删除没有查询出来的数据的问题
    * 现在不再分页
    * 会增量更新发生变化的数据
8.  优化角色删除逻辑
    * 现在会一同删除角色附加的资源
        * 用户分配角色
        * 角色分配的功能
9.  修复html 自动映射 url 的问题
    * 当 url 有4段时,无法正确映射 url
10.  修复后台解析日期字符串的问题
    * 现在对于空字符串,null 等值,解析时不再报错
11.  修复配置维护的若干问题
    * 修改ProfileMapper.xml ,继承错误
    * mysql 中,主键被错误的定义为 VARCHAR
    * sql 语句包含了 oracle 特有函数
    * 查询排序不唯一的问题
12.  修复计划任务查询的问题
    * 当任务没有执行过时,无法查到这个任务
13.  修复 oracle 数据库插入/更新 null 值可能会报错的问题
    * 添加配置文件,jdbcTypeForNull 为 NULL
14.  修复功能定义循环,无明显错误提示的问题
    * 现在在功能分配时,会有一个很明显的错误提示,指出定义出错的地方
15.  修复无法上传特殊类型附件的问题
    * 去除 contentType 验证
16.  sys_user 表 user_name 不再区分大小写
    * 现在新增用户不在要求用户名必须大写
    * 登录时也不做要求


API变更
---
1.  IBaseService  ~~selectAll()~~
    * 该方法已不推荐使用,无法正确查询多语言数据
    * 新的方法为  `selectAll(IRequest)`
2.  BaseServiceImpl 新增 `boolean useSelectiveUpdate()`
    * 返回值将决定:在 `batchUpdate` 方法中,对于更新操作,使用的 `updateByPrimaryKey` 还是 `updateByPrimaryKeySelective`
3.  新增注解`@ExtensionAttribute(disable=true)`
    * 添加在 DTO 类上,允许相关表没有扩展字段
4.  新增注解`@FreeMarkerBean`
    * 具有这个注解并在 spring context 中存在的 bean,将可以在 freemarker 标记中使用
5.  新增接口`IAuthenticationSuccessListener`
    * 实现此接口并在spring context 中存在的 bean,将会在登录成功以后被调用
    * 可以在此阶段执行一些操作
    * 可以通过抛出异常的方式终止登录
6.  增加 `SysConfigListener` 接口
    * 匹配新增加的 `系统配置功能`
    * 用法与 `GlobalProfileListener` 相同
7.  增加`SqlMapper` 工具类
    * 用于手动执行 带有参数标签的 sql
8.   工作流:增加接口`IActivitiBean`
    * 用于自定义可在流程运行时调用的 bean
9.  工作流:增加接口 `IUserTaskNotifier`
    * 用于自定义待办事项通知



工作流
---
1.  基于 Activiti 6 Beta4
2.  基础功能
    * 工作流测试
    * 代办事项(列表,明细页面)
    * 历史流程查看
    * 流程设计(列表,导入导出,设计器)
    * 部署管理
3.  支持嵌入自定义展示页面
    * 通过 `businessKey` 参数传递业务主键
    * 自定义页面可以获取当前任务的信息
    * 自定义页面可以设置变量到流程中
4.  支持自定义审批按钮
    * 通过约定的表单属性,可以控制审批按钮是否存在,以及按钮显示的文本
5.  自定义可在流程中调用的bean 
    * 自定义`IActivitiBean` 接口
    * 实现此接口的 bean,可以在流程中直接调用
6.  支持代办事项的通知
    * 自定义`IUserTaskNotifier` 接口
    * 实现此接口bean 的实例,将会在任务创建时被调用
7.  支持审批意见,审批动作的保存
    * 可以在审批历史中看到具体的操作,以及意见
8.  会签功能增强, 支持自定义的属性
    * `nrOfApproved`: 已审批通过的数量
    * `nrOfRejected`: 已审批拒绝的数量
9.  支持流程的转交
10.  支持节点跳转
    * 在 API 级别,支持跳转到指定的节点
11.  审批权限控制
    * 管理员可以执行任何操作
    * 其他人执行操作(查看,审批,转交等)时,会经过控制
12.  自定义人员与组的支持
    * 与组织架构紧密结合
    * 流程中指定的人,组均为组织架构中实际存在的数据
13.  多个常见 demo
    * 普通审批节点,页面嵌入
    * 分支控制
    * 表达式判断
    * 会签
    * 外部消息接收
    * Service 任务
    * 邮件
