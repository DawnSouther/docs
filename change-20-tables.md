本文讲述 HAP 2.0 相对于  1.0 的表结构变化.

## SYS_USER

1. 增加 LAST_LOGIN_DATE
  * 该字段用来记录用户最后一次登录成功的时间

2. 增加 LAST_PASSWORD_UPDATE_DATE
  * 该字段用于记录用户最后一次修改密码的时间
  * 与 `密码过期机制` 相对应

- 其他变化
    * USER_NAME  字段现在不要求必须大写

## SYS_ROLE_B

1. 增加字段 START_ACTIVE_DATE
  * 角色开始有效的日期, 为空 表示没有限制    
  * 在该日期之前, 角色不可用

2. 增加字段 END_ACTIVE_DATE
  * 角色截止有效的日期, 为空 表示没有限制
  * 超过该日期后, 角色不可用

3. 增加字段 ENABLE_FLAG (Y/N)
  * 是否启用该角色
  * 默认 Y, 为 N 时, 角色不可用

## SYS_PREFERENCE

1. 删除字段 ~~PREFERENCE_LEVEL~~

## 新增 SYS_CONFIG 表

用于保存系统级的配置信息. 页面请参见 `系统管理 - 系统设置`

目前保存的信息有:

1. 系统 Title
2. 验证码策略
3. 新用户默认密码
4. 密码失效时间
5. 密码最小长度
6. 密码复杂度(验证规则)

## 新增 SYS_USER_LOGIN 表

用于记录用户的登录

## 新增 SYS_DASHBOARD_B(TL)

用于保存用户自定义的 bashboard 配置

## 新增 HR_XX 相关表

基础的组织架构表, 工作流会用到
