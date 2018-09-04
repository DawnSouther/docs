
# DatePicker标签
日期选择器


```xml
 <h:datePicker id="datePicker"/>
```
### **主要属性**





属性名    | 类型
-------- | -----
culture | String
depth| String
footer| String \| Function
format| String
max| String
min| String
start| String
value | String
name | String
bind | Function
required | Boolean
change | Function
close| Function
open | Function
placeholder | String
> **提示:** 属性用法请参考kendoui API文档 http://docs.telerik.com/kendo-ui/api/javascript/ui/datepicker

### **用法示例**

```javascript
//注意是写在script标签内的
var viewModel = kendo.observable({
    data:{}
});

function open(){
//...
}

function footer(){
//...
}
```

```xml
<h:datePicker id="datepicker" 
              max="2016-10-14" 
              name="datepicker" 
              placeholder="datepicker" 
              bind="value:data.birthday" 
              required="true" open="open" footer="fn:footer"/>
```



#### **子标签**

属性名    | 类型
-------- | ---
animation | Object
month| Object

animation | 类型 
------ |----
close | Object 
open  | Object

```xml
<h:datePicker id="datePicker">
    <h:animation>
       <!--当属性仍然为一个对象时，继续添加子标签-->
       <h:close effects="zoom:out" duration="300"/>
       <h:open effects="zoom:in" duration="300"/>
    </h:animation>
</h:datePicker>
```

month | 类型
--------|------
content | String
empty| String

```xml
<h:datePicker id="datePicker">
    <h:month content="<div></div>" empty="-"/>
</h:datePicker>
```

>**提示**：

> - 遇到**object**类型 就向下添加子标签，当遇到普通类型时，就添加到当前的标签上。
