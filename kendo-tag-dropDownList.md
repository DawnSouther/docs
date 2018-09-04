
DropDownList
-------------
下拉选择框

##### xml配置

```xml
 <!--h:为命名空间,必须要加的;dropDownList为组件名;id可写可不写-->
 <h:dropDownList id="dropDownList"/>
```

#### **一般属性**


> **提示:**

> -  属性用法请参考kendoui API文档的dropdownlist:http://docs.telerik.com/kendo-ui/api/javascript/ui/dropdownlist


属性名    | 类型
-------- | ---
animation | Boolean
autoBind | Boolean
autoWidth | Boolean
cascadeFrom| String
cascadeFromField | String
clearButton| Boolean 
dataSource | kendo.data.DataSource(Function)
dataTextField | String
dataValueField| String
delay | Number
enable | Boolean
enforceMinLength| Boolean
filter | String
fixedGroupTemplate| String & Function
footerTemplate| String & Function
groupTemplate| String & Function
height| Number
ignoreCase| Boolean
index| Number
minLength| Number
noDataTemplate| String & Function
optionLabel |String
optionLableTemplate| String & Function
placeholder | String
headerTemplate| String & Function
template| String & Function
valueTemplate | String & Function
text| String
value| String
valuePrimitive| Boolean
virtual| Boolean
change| Function
close| Function
dataBound | Function
filtering| Function
open| Function
select| Function
cascade| Function

用法示例：

```javascript
//注意是写在script标签内的
var dataSource= new kendo.data.DataSource({
    data:["one","two"]
});

var template = function (){
//...
}

//var change =function(){
//...
//}
function change(){
//...
}
//以上两种函数定义方法皆可
```

```xml
<h:dropDownList id="dropDownList" dataSource="dataSource" clearButton="true" placeholder="test" enable="false" template="fn:template" change="change"/>
```

> **提示:**

> - 上述所有属性直接添加在标签内即可，（**[属性名] = "..."**）,对于既是**String**类型又是**Function**类型的属性，只需要加**fn:**前缀区分即可,（**fn:函数名**）


#### **子标签**

属性名    | 类型
-------- | ---
animation | Object
popup | Object
virtual| Object

animation | 类型 
------ |----
close | Object 
open  | Object

```xml
<h:dropDownList id="drowDownList">
    <h:animation>
       <!--当属性仍然为一个对象时，继续添加子标签-->
       <h:close effects="zoom:out" duration="300"/>
       <h:open effects="zoom:in" duration="300"/>
    </h:animation>
</h:dropDownList >
```

popup   | 类型
--------|------
appendTo| String
origin  | String
position| String

```xml
<h:dropDownList id="dropDownList">
    <h:popup appendTo="container" origin="top left" position="top left"/>
</h:dropDownList >
```


virtual    | 类型
-----------|-----
itemHeight | Number
mapValueTo | String
valueMapper| Function


```javascript
function valueMapper(){
//...
}
```

```xml
<h:dropDownList id="dropDownList">
    <h:virtual itemHeight ="20" mapValueTo="index" valueMapper="valueMapper"/>
</h:dropDownList >
```

>**提示**：

> - 遇到**object**类型 就向下添加子标签，当遇到普通类型时，就添加到当前的标签上。
