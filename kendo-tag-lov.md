# LOV标签

```xml

//code、locale、contenxtPath为必须的参数，其中code为所需lov在lov定义时的code的值

 <h:lov id="lov" code="LOV_ROLE" locale="${base.locale}" contextPath="${base.contextPath}" />

```


#### **必须参数**

参数名|类型/值
----|----
code|String
locale|String (一般为${base.locale})
contextPath|String（一般为${base.contextPath}）



#### **一般属性**

属性名|类型
----|----
name|String
enabled|Boolean
valuePrimitive|Boolean
text|String
dataTextField|String
dataValueField|String
open|function
close|function
select|function
query|function





用法示例：

```javascript
//以下代码写在script标签内
function lovQuery(e) {
       //...
      };
function lovSelect(e) {
      //...
      };
```



```xml
<!--xml代码-->
<h:lov id="lov" name="lov" placeholder="lov" bind="enabled: isEnabled, value:data.userId,text:data.userName" style="width: 100%" code="LOV_ROLE" locale="${base.locale}" contextPath="${base.contextPath}" query="lovQuery" select="lovSelect" />

```


> **提示:**



> - 上述所有属性直接添加在标签内即可，（**[属性名] = "..."**）,对于既是**String**类型又是**Function**类型的属性，只需要加**fn:**前缀区分即可,（**fn:函数名**）







#### **其他(bind required)**
用法：
```xml
 <!--bind 相当于data-bind的作用-->
 <h:lov id="combobox" bind="model.functionId" required="true" code="LOV_ROLE" locale="${base.locale}" contextPath="${base.contextPath}" />

```
