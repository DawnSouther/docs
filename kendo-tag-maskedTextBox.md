
MaskedTextBox
-------------
格式框

##### xml配置

```xml
 <!--h:为命名空间,必须要加的;maskedTextBox为组件名;id可写可不写-->
 <h:maskedTextBox id="maskedTextBox"/>
```

#### **一般属性**


> **提示:**

> -  属性用法请参考kendoui API文档的maskedTextBox:http://docs.telerik.com/kendo-ui/api/javascript/ui/maskedtextbox


属性名    | 类型
-------- | ---
clearPromptChar | Boolean
culture| String
mask | String
promptChar | String
unmaskOnPost | Boolean 
value | String
change | Function
caseLetter | String
clearButton | Boolean
placeholder | String
bind | Function
required | Boolean

用法示例：

```xml
<h:maskedTextBox id="maskedtTextBox" required="true" placeholder="maskedTextBox" clearButton="true"/>

```
> **提示:**

> - 上述所有属性直接添加在标签内即可，（**[属性名] = "..."**）


#### **子标签**

属性名    | 类型
-------- | ---
rules| Object

```javascript
function rules(){
//...
}
```

```xml
<h:maskedTextBox id="maskedtTextBox">
    <h:rules  "~"="/[+-]/"  "^"="fn:rules"/>
</h:maskedTextBox>
```

>**提示**：

> - 遇到**object**类型 就向下添加子标签，当遇到普通类型时，就添加到当前的标签上。
