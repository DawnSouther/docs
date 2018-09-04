
# DateTimePicker标签

时间选择器(带时分秒)


### **主要属性**

属性名    | 类型
-------- | -----
animation | Boolean
ARIATemplate| String
culture | String
depth| String
footer| String & Function
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
interval |Integer
timeFormat |String
> **提示:** 属性用法请参考kendoui API文档 http://docs.telerik.com/kendo-ui/api/javascript/ui/datetimepicker


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
<h:dateTimePicker id="dateTimePicker" 
                  max="2016-10-14"  
                  placeholder="dateTimePicker" 
                  bind="value:data.birthday" 
                  required="true" open="open" 
                  footer="fn:footer" timeFormat="HH:mm"/>
```

> **提示:**

> - 上述所有属性直接添加在标签内即可，（**[属性名] = "..."**）,对于既是**String**类型又是**Function**类型的属性，只需要加**fn:**前缀区分即可,（**fn:函数名**）
> - 对于**max min**等本该是**时间类型**的属性，改为日期字符串，且连接符号为"**-**"


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
<h:dateTimePicker id="dateTimePicker">
    <h:animation>
       <!--当属性仍然为一个对象时，继续添加子标签-->
       <h:close effects="zoom:out" duration="300"/>
       <h:open effects="zoom:in" duration="300"/>
    </h:animation>
</h:dateTimePicker>
```

month | 类型
--------|------
content | String
empty| String

```xml
<h:dateTimePicker id="dateTimePicker">
    <h:month content="<div></div>" empty="-"/>
</h:dateTimePicker >
```

>**提示**：

> - 遇到**object**类型 就向下添加子标签，当遇到普通类型时，就添加到当前的标签上。
