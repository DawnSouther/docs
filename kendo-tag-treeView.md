
TreeView
-------------
树视图

##### xml配置

```xml
 <!--h:为命名空间,必须要加的;treeView为组件名;id可写可不写-->
 <h:treeView id="treeView"/>
```

#### **一般属性**


> **提示:**

> -  属性用法请参考kendoui API文档的treeView: http://docs.telerik.com/kendo-ui/api/javascript/ui/treeview

属性名    | 类型
-------- | ---
animation | Boolean
autoBind | Boolean
autoScroll | Boolean
checkboxes | Boolean
dataImageUrlField| String
dataSource | kendo.data.HierarchicalDataSource(Function)
dataSpriteCssClassField | String
dataTextField | String & Array
dataUrlField | String 
dragAndDrop| Boolean
loadOnDemand| Boolean
treeListView| Number
template | String & Function
change| Function
check | Function
collapse | Function
dataBound | Function
dragstart | Function
drag | Function
dragend | Function
drop | Function
expand | Function
navigate | Function
select | Function
用法示例：

```javascript
//注意是写在script标签里的内容
var dataSource = new kendo.data.HierarchicalDataSource({
  data: [
      { text: "foo", items: [
        { text: "bar" }
      ] }
    ]
});

function template(){
//...
}
```

```xml
<h:treeView id="treeView" dataSource="dataSource" checkboxes = "true" dataTextField = "name1,name2" template="fn:template"/>

```
> **提示:**

> - 上述所有属性直接添加在标签内即可，（**[属性名] = "..."**）,对于类型是函数的属性，需要另外在script标签中具体定义函数，这里只是一个引用,对于类型**既是函数又是字符串**类型的属性，一定要加**fn:** 来区分，加fn:的代表这是一个函数，对于**既是字符串又是数组**的属性，数组内容只需要用**逗号分割**即可

#### **子标签**

属性名    | 类型
-------- | ---
animation | Object
checkboxes | Object
messages | Object

animation | 类型 
------ |----
close | Object 
open  | Object

```xml
<h:treeView id="treeView">
    <h:animation>
       <!--当属性仍然为一个对象时，继续添加子标签-->
       <h:close effects="zoom:out" duration="300"/>
       <h:open effects="zoom:in" duration="300"/>
    </h:animation>
</h:treeView>
```

checkboxes | 类型
--------|------
checkChildren | Boolean
name | String
template | String & Function

```xml
<h:treeView id="treeView">
   <!--template的前缀fn: 表明此时是函数-->
    <h:checkboxes checkChildren="true" name="checkedItems[]" template="fn:template"/>
</h:treeView >
```


messages | 类型
-----------|-----
loading | String
requestFailed | String
retry | String

```javascript
function template(){
//...
}
```

```xml
<h:treeView id="treeView">
   <!--template的前缀fn: 表明此时是函数-->
    <h:messages loading="loading..." requestFailed="fail..." retry="retry"/>
</h:treeView >
```

>**提示**：

> - 遇到**object**类型 就向下添加子标签，当遇到普通类型时，就添加到当前的标签上,就像处理一般属性那样。
