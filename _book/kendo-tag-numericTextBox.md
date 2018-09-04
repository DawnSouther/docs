
NumericTextBox
-------------
数字框

##### xml配置

```xml
 <!--h:为命名空间,必须要加的;numericTextBox为组件名;id可写可不写-->
 <h:numericTextBox id="numericTextBox"/>
```

#### **一般属性**


> **提示:**

> -  属性用法请参考kendoui API文档的numericTextBox:http://docs.telerik.com/kendo-ui/api/javascript/ui/numerictextbox


属性名    | 类型
-------- | ---
culture| String
decimals | Number
downArrowText | String
format | String
max | Number
min | min
restrictDecimals | Boolean
round | Boolean
spinners | Boolean 
step | Number
upArrowText | String
spin | Function
value | Number
change | Function
clearButton | Boolean
placeholder | String
bind | Function
required | Boolean

用法示例：

```javascript
//注意是写在script标签里的内容
function change(){
//...
}
```

```xml
<h:numericTextBox id="numericTextBox" max="20" min="10" step="1" value="12" clearButton="true" change="change"/>

```
> **提示:**

> - 上述所有属性直接添加在标签内即可，（**[属性名] = "..."**）,对于类型是函数的属性，需要另外在script标签中具体定义函数，这里只是一个引用

