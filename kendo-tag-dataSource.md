# dataSource标签

数据源组件

```xml
<h:dataSource id="gridDataSource" batch="true" pageSize="10" serverPaging="true" error="requestError"> 
  <h:transport parameterMap="parameterMap"> 
    <h:read url="${base.contextPath}/sys/resource/query" type="POST" dataType="json"/>  
    <h:create url="${base.contextPath}/sys/resource/submit" type="POST" contentType="application/json"/>  
    <h:update url="${base.contextPath}/sys/resource/submit" type="POST" contentType="application/json"/>  
    <h:destroy url="${base.contextPath}/sys/resource/remove" type="POST" contentType="application/json"/> 
  </h:transport>  
  <h:schema data="rows" total="total" errors="schemaError"> 
    <h:model id="resourceId" editable="dsEditable"> 
      <h:fields> 
        <h:item name="loginRequire" defaultValue="Y" type="boolean" checkedValue="Y" uncheckedValue="N"/>  
        <h:item name="accessCheck" defaultValue="Y" type="boolean" checkedValue="Y" uncheckedValue="N"/>  
        <h:item name="type" defaultValue=""/>  
        <h:item name="url"> 
          <h:validation required="true"/> 
        </h:item>  
        <h:item name="name"> 
          <h:validation required="true"/> 
        </h:item> 
      </h:fields> 
    </h:model> 
  </h:schema> 
</h:dataSource>

```



### **主要属性**


属性名 | 类型
-------- | ---
data|Array & String
autoSync|Boolean
batch|Boolean
page|Integer
pageSize|Integer
serverAggregates|Boolean
serverFiltering|Boolean
serverGrouping|Boolean
serverPaging|Boolean
serverSorting|Boolean
type|String
change|Function
error|Function
push|Function
requestEnd|Function
requestStart|Function
sync|Function
> **提示:** 属性用法请参考kendoui API文档 http://docs.telerik.com/kendo-ui/api/javascript/data/datasource

### **主要子节点**
* transport
* schema
* sort


### **transport**
服务端交互配置
#### **transport属性**

属性名|类型
------ |----
parameterMap | Function
push|Function
```xml
<h:dataSource id="gridDataSource" > 
  <h:transport parameterMap="parameterMap"> 
    ...
  </h:transport>
</h:dataSource>
```
#### **transport子节点**
主要有以下几种节点类型
* read
* create
* update
* destroy

属性名|类型
----|----
cache|Boolean
contentType |String
data|Object \| Function
dataType|String
type|String
url| String \| Function

```xml
  <h:transport parameterMap="parameterMap"> 
    <h:read url="${base.contextPath}/sys/resource/query" type="POST" dataType="json"/>  
    <h:create url="${base.contextPath}/sys/resource/submit" type="POST" contentType="application/json"/>  
    <h:update url="${base.contextPath}/sys/resource/submit" type="POST" contentType="application/json"/>  
    <h:destroy url="${base.contextPath}/sys/resource/remove" type="POST" contentType="application/json"/> 
  </h:transport> 
```



### **schema**
定义datasource的数据结构


#### **schema属性**

属性名|类型
----|----
data|String \| Function
errors|String \| Function
groups|String \| Function
parse|Function
total|String \| Function
type|String

```xml
<h:dataSource id="gridDataSource" > 
  <h:schema data="rows" total="total" errors="schemaError">  
    ...
  </h:schema>
</h:dataSource>

```

#### **schema子节点**
* model

属性名|类型
----|----
id| String
editable|Function

```xml
<h:schema data="rows" total="total" errors="schemaError"> 
    <h:model id="resourceId" editable="dsEditable"> 
      <h:fields> 
         ... 
      </h:fields> 
    </h:model> 
  </h:schema> 
```
#### **model子节点**
* field

属性名|类型
----|----
name| String
defaultValue| Object
editable | Boolean
nullable|Boolean
parse | Function
type | String
from | String
checkedValue | String
uncheckedValue | String

```xml
<h:schema data="rows" total="total" errors="schemaError"> 
    <h:model id="resourceId" editable="dsEditable"> 
      <h:fields> 
        <h:item name="loginRequire" defaultValue="Y" type="boolean" checkedValue="Y" uncheckedValue="N"/>  
        <h:item name="accessCheck" defaultValue="Y" type="boolean" checkedValue="Y" uncheckedValue="N"/>  
        <h:item name="type" defaultValue=""/>  
        <h:item name="url"> 
          <h:validation required="true"/> 
        </h:item>  
        <h:item name="name"> 
          <h:validation required="true"/> 
        </h:item> 
      </h:fields> 
    </h:model> 
  </h:schema> 
```
#### **field子节点**
* validation

属性名|类型
----|----
required| Boolean
max| Integer
min| Integer


### **sort**

属性名|类型
----|----
dir|String
field|String
compare|function

```xml
    <h:dataSource id="gridDataSource" batch="true" pageSize="10" serverPaging="true" serverSorting="true" error="requestError">
      <h:transport parameterMap="parameterMap">
        ...
      </h:transport>
      <h:schema data="rows" total="total" errors="schemaError">
        <h:model id="resourceId" editable="dsEditable">
           <h:fields>
               ...
           </h:fields>
        </h:model>        
      </h:schema>
      <h:sort>
            <h:item field="url" dir="desc"/>
      </h:sort>
    </h:dataSource>
```




