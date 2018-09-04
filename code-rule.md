# 编码生成器

# 简介

3.1.8-RELEASE版本以后新增功能，支持定义编码规则，包括序列，日期，常量，动态参数，UUID等，通过编码code获取生成好的编码。

## 功能入口

**系统管理->编码规则**

## 使用说明

规则定义界面

<img width='920' src='/assets/codeRule_list.png'/>

> 已经启用的规则不可以编辑，只可以查看！

<img width='920' src='/assets/code_rule_edit.png'/>

> 如果没有更新序列配置，重新启用编码规则并不会重置序列。反之序列会重新生成。

**段类型说明**

* 序列：利用redis实现的自增序列，需要设置序列位数与开始值，可以设置重置频率（每年，每季度，每月），在指定周期内会重置序列。

> 序列每自增100会更新当前值，当前值可以反应当前序列范围：比如当前值是1200，那么此时序列范围是1100-1200

* 常数：一个固定的字符串

* 变量：动态参数，使用freemaker替换变量，${xxx}形式

* 日期： 根据日期掩码拼接指定格式的日期

* UUID： 生成一段32位长度的UUID

**调用说明**

在service中注入ICodeRuleProcessService：

```java

  //...
  @Autowired
  ISysCodeRuleProcessService codeRuleProcessService
  //...
  //设置编码
  xxx.setXXX(codeRuleProcessService.getRuleCode("编码code"));

  //使用动态参数，需要传入一个Map
  //比如编码定义了一个变量${var}
  Map<String,String> map = new HashMap<>();
  map.put("var","demo");
  xxx.setXXX(codeRuleProcessService.getRuleCode("编码code",map));

```

> 使用动态参数时，如果没有传入包含该参数的map，依然会正常生成编码,动态参数的位置将为空！
