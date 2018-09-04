treeList

-------------
树列表



##### xml配置

```xml

 <!--h:为命名空间,必须要加的;treeList为组件名;id可写可不写-->

<h:treeList id="testTreeList">
    ...
</h:treeList>

```



#### **一般属性**

> **提示:**
> - 属性用法请参考kendoui API文档的treeList: http://docs.telerik.com/kendo-ui/api/javascript/ui/treelist


属性名 | 类型
-------- | ---
columnMenu| Boolean
autoBind | Boolean
dataSource | kendo.data.TreeListDataSource
resizable|Boolean
reorderable|Boolean
editable |Boolean
height|Integer & String
scrollable |Boolean
selectable |Boolean & String 
sortable |Boolean
autoScroll | Boolean
toolbar |String & function
cancel|function
change| Function
columnHide |Function
columnMenuInit |Function
columnReorder | Function
columnResize | Function
columnShow | Function
columnLock |function
columnUnlock |function
dataBinding | function
dataBound | Function
dragstart | Function
drag |function
dragend |function
drop |function
edit | Function
excelExport | Function
expand |function
filterMenuInit |function
pdfExport | Function
remove | Function
save | Function


用法示例：



```javascript
//注意是写在script标签里的内容
var treeListData = new kendo.data.TreeListDataSource({
    "data":[{"name":"z","age":"12"},
            {"name":"t","age":"16"}]
});
function test(){
    ...
}
```


```xml
<h:treeList id="testTreeList" dataSource="treeListData" editable="true">
     <h:columns>
         <h:column field="name" width="300" editor="test"</h:column>
         <h:column field="age" width="200"></h:column>
         <h:column>
             <h:command>
                 <h:item click="test" name="detail"/>
                 <h:item click="test" name="read"/>
             </h:command>
         </h:column>
     </h:columns>
 </h:treeList>
```

> **提示:**



> - 上述所有属性直接添加在标签内即可，（**[属性名] = "..."**）,对于类型是函数的属性，需要另外在script标签中具体定义函数，这里只是一个引用,对于类型**既是函数又是字符串**类型的属性，一定要加**fn:** 来区分，加fn:的代表这是一个函数，对于**既是字符串又是数组**的属性，数组内容只需要用**逗号分割**即可





#### **子标签**

属性名 | 类型
-------- | ---
columns| Array
editable|Object
messages |Object
scrollable |Object
sortable |Object
toolbar |Array


###### **columns**子标签
属性名|类型
----|----
attributes| Object
command | Object
editor | Function
encoded | Boolean
expandable| Boolean
field|String
filterable|Boolean & Object
footerTemplate| String & Function
format|String
headerAttributes| Object
headerTemplate| String & Function
minScreenWidth| Integer
sortable|Object
template| String & Function
title| String
width |Integer & String
hidden|Boolean
menu|Boolean
locked|Boolean
lockable|Boolean
>template的类型既可以是Sting，有可以是function，则当使用类型为function时，需要function名的的前面加```fn:```前缀
```javacript
//script标签内的example函数
function example(){...}
```
```xml
 <h:xxx>
    ...
 <!--template的前缀fn: 表明此时是函数，example为script中定义的函数名-->
    <h:xxx template="fn:example" />
    ...
</h:xxx>
```

######**editable**子标签

属性名|类型
----|----
mode|String
move|Boolean
template|String & function
window|Object




######**messages**子标签

属性名|类型
----|----
commands |Object
loading |String
noRows |String
requestFailed |String
retry |String
>commands对象的属性可参考kendoUi官网的API


######**sortable**子标签

属性名|类型
----|----
allowUnsort |Boolean
mode|String


######**toolbar**子标签

属性名|类型
----|----
click |function
name|String
text|String





>**提示**：



> - 遇到**object**类型 就向下添加子标签，当遇到普通类型时，就添加到当前的标签上,就像处理一般属性那样。


