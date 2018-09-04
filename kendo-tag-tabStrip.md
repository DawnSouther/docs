tabStrip

-------------

标签页



##### xml配置



```xml

<!--h:为命名空间,必须要加的;tabStrip为组件名;id可写可不写;value为默认打开的标签页，属性值为tab页的title的值-->
 <h:tabStrip id="tabstrip" value="Paris" animation="false">
      <h:tabs>
            //title为标签页的标题
          <h:tab title="Paris">
            //此标签的html内容  
             ...
          </h:tab>
          <h:tab title="New York">
            ...
          </h:tab>
        </h:tabs>
</h:tabStrip>

```



#### **一般属性**

> **提示:**
> - 属性用法请参考kendoui API文档的tabStrip:http://docs.telerik.com/kendo-ui/api/javascript/ui/tabstrip


属性名|类型
----|----
animation|Boolean
collapsible|Boolean
dataContentField|String
dataSource | kendo.data.DataSource(Function)
dataTextField|String
navigatable|true
scrollable|Boolean
tabPosition|String
value|String
activate|function
contentLoad|function
error|function
select|function
change| Function
dataBound | Function
dataBinding|function



用法示例：

```xml
<h:tabStrip value="标签2" collapsible="true" tabPosition="top">
     <h:tabs>
         <h:tab title="标签1">
             <h:button text="hello1" className="btn-primary" click="test" enable="true" icon="cancel"/>
             <h:button text="hello2" className="btn-primary" click="test" enable="true" icon="cancel"/>
         </h:tab>
         <h:tab title="标签2">
             <h:button text="hello3" className="btn-primary" click="test" enable="true" icon="cancel"/>
             <h:button text="hello4" className="btn-primary" click="test" enable="true" icon="cancel"/>
         </h:tab>
     </h:tabs>
 </h:tabStrip>


<!--或者-->

<h:dataSource id="dataSourceTabStrip">
     <h:data>
         <h:item name="tab1" content="tab1content"/>
         <h:item name="tab2" content="tab2content"/>
     </h:data>
 </h:dataSource>

 <h:tabStrip dataSource="dataSourceTabStrip" dataTextField="name" dataContentField="content" />




```

> **提示:**



> - 上述所有属性直接添加在标签内即可，（**[属性名] = "..."**）,对于既是**String**类型又是**Function**类型的属性，只需要加**fn:**前缀区分即可,（**fn:函数名**）





#### **子标签**



属性名 | 类型
-------- | ---
animation | Object
scrollable| Object

###### **animation**子标签

属性名| 类型
------ |----
close | Object
open | Object


###### **scrollable**子标签

属性名|类型
----|----
distance|Integer

用法举例：
```xml
<h:tabStrip dataSource="dataSourceTabStrip" dataTextField="name" dataContentField="content">
     <h:scrollable distance="100" />
    <h:animation>
         <!--当属性仍然为一个对象时，继续添加子标签-->
         <h:close effects="zoom:out" duration="300"/>
         <h:open effects="zoom:in" duration="300"/>
     </h:animation>

</h:tabStrip>

```

>**提示**：



> - 遇到**object**类型 就向下添加子标签，当遇到普通类型时，就添加到当前的标签上。


