QueryPanel
-------------
高级查询

##### xml配置

```xml
<!--h:为命名空间,必须要加的;queryPanel为组件名;id可写可不写-->
<h:queryPanel id="queryPanel"/>
```

#### **一般属性**

属性名    | 类型
--------------| -----
queryFunction | Function
resetFunction | Function

用法示例：

```javascript
//注意是写在script标签内的
var viewModel = kendo.observable({
    data:{},
    queryFunction:function(e){
    },
    resetFunction:function(e){
    }    
});
```

```xml
<h:queryPanel id="queryPanel" queryFunction="viewModel.queryFunction" resetFunction="viewModel.resetFunction"/>
```


#### **子标签**

属性名    | 类型
--------------| -----
common| Object
advance| Object

```xml
<h:queryPanel id="queryPanel">
   <h:common>
      <!--自定义区域 简单查询内容-->
      <h:maskedTextBox id="maskedTextBox1"/>
   </h:common>
   <h:advance>
      <!--自定义区域 高级查询内容-->
      <h:maskedTextBox id="maskedTextBox2"/>
   </h:advance>
</h:queryPanel>
```

> **提示：**

> - 自定义区域内用来定义新的标签 **common** 和 **advance**标签内都属于自定义标签范围