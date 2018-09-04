# 数据屏蔽

3.1.9-RELEASE之后新增功能。可在用户，角色等范围过滤数据，控制对数据的查看范围。

## 1 功能入口

**系统管理-&gt;数据屏蔽**

## 2 使用说明

### 2.1 定义规则

示例 定义了3条规则，屏蔽范围为所要屏蔽的数据对象。及定义组织屏蔽模型，系统初始化用户，角色，自定义SQL对象，可在代码维护（SYS.AUTHORITY\_SEGMENT）中配置，代码维护value为LOV CODE。

![](/assets/permission-rule1.png)

### 2.2 定义规则项

为规则定义具体的屏蔽模型及从规则字段选取的模型中选取具体数据，示例是选取3条组织数据作为屏蔽模型。2图为位自定义SQL页面。

### ![](/assets/permission-rule-detail.png)![](/assets/permission-rule-detail_sql.png)2.3 分配屏蔽规则

系统初始化用户角色，可在代码维护（SYS.AUTHORITY\_ROLE）里配置![](/assets/permission-rule-assign.png)配置之后需编写拦截器 继承com.hand.hap.security.permission.service.impl.DataPermissionRangeFilter

##### 示例用户拦截器：

```
@Component
public class UserMaskRangeFilter extends DataPermissionRangeFilter {
    @Autowired
    DataPermissionCacheContainer container;

    //对应SYS.AUTHORITY_ROLE代码维护中value
    private final String maskRange = "user_lov";

    public void doFilter(IRequest iRequest, String tableName, Map data) throws ExecutionException {
        if (iRequest.getUserId().toString().equals("-1"))
            return;

        List<DataPermissionTableRule> ruleCodeList = container.getTableRule(tableName);
        if (ruleCodeList.size() == 0)
            return;

        //根据userId获取屏蔽规则
        setRuleDetail(ruleCodeList, maskRange, iRequest.getUserId().toString(), container, data);

    }
}
```

### 2.4 数据屏蔽设置

选择一个表并为其分配屏蔽规则。相同字段间进行规则或操作，不同时进行与操作，自定义SQL进行与操作。![](/assets/permission-table.png)

## 3 示例演示

当给表分配一个规则（包含3条数据）时用户查询组织表只有3条数据

![](/assets/permission-demo1.png)![](/assets/permission-demo2.png)当为其多分配第二条规则（包含2条规则）时可以看到查出了5条数据

![](/assets/permission-demo3.png)![](/assets/permission-demo4.png)

