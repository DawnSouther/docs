## Grid

#### 表格

对于API文档中的所有属性，考虑实际需要，有些属性没必要实现，本文档若未列出，就代表并未实现

#### xml配置

```xml
 <!--h:为命名空间,必须要加的;grid为组件名;id可写可不写-->
 <h:grid id="grid"/>
```

```xml
<!--grid-->
<h:grid dataSource="dataSource" selectable="multiple, rowbox" editable="true">
    <h:pageable pageSizes="5, 10, 20, 50" buttonCount="5" refresh="true">
    </h:pageable>
    <h:columns>
        <h:column field="moduleCode" title='' width="100"/>
        <h:column field="functionCode" title='' width="180"/>
    </h:columns>
</h:grid>
```

---

对于所有属性，无论是直接属性，还是子标签的属性，都遵循以下原则：
1.若是简单的属性，就添加到当前标签上，若是对象，就创建子标签，这里的简单属性是指能直接被描述在当前标签上的属性，如字符串，函数，\(函数只是一个引用\)，布尔类型，数字类型等等，对象是指这个属性又包含了子属性，所以我们不得不向下创建子标签才能将其子属性描述出来。
2.对于String和Function类型，必须用fn：前缀加以区分，
3.对于数组，有些数组是字符串数组，这种直接当作简单的属性处理，用逗号分隔每个字符串即可，有些数组是list集合数组，这种必须先创建属性子标签，再创建tem子标签来描述集合，详情可参考子标签columns

---

### **一般属性**

> **提示:**
> 
> * 属性用法请参考kendoui API文档的grid: [http:\/\/docs.telerik.com\/kendo-ui\/api\/javascript\/ui\/gird](http://docs.telerik.com/kendo-ui/api/javascript/ui/gird)

| 属性名 | 类型 |
| --- | --- |
| allowCopy | Boolean |
| altRowTemplate | String & Function |
| autoBind | Boolean |
| columnResizeHandleWidth | Integer |
| columnMenu | Boolean |
| dataSource | kendo.data.DataSource\(Function\) |
| detailTemplate | String & Function |
| editable | Boolean & String |
| groupable | Boolean |
| height | Integer & String |
| mobile | Boolean & String |
| navigatable | Boolean |
| noRecords | Boolean |
| pageable | Boolean |
| reorderable | Boolean |
| resizable | Boolean |
| rowTemplate | String & Function |
| scrollable | Boolean |
| selectable | Boolean & String |
| sortable | Boolean |
| toolbar | String & Function |
| cancel | Function |
| change | Function |
| columnHide | Function |
| columnMenuInit | Function |
| columnReorder | Function |
| columnResize | Function |
| columnShow | Function |
| dataBinding | Function |
| dataBound | Function |
| detailCollapse | Function |
| detailExpand | Function |
| detailInit | Function |
| edit | Function |
| excelExport | Function |
| filter | Function |
| group | Function |
| page | Function |
| pdfExport | Function |
| filterMenuInit | Function |
| remove | Function |
| save | Function |
| saveChanges | Function |
| sort | Function |
| columnLock | Function |
| columnUnlock | Function |
| navigate | Function |

用法示例：

```javascript
//注意是写在script标签里的内容
var dataSource = new kendo.data.DataSource({
    data: [
        { name: "Jane Doe", age: 30 },
        { name: "John Doe", age: 33 }
    ]
});

function edit(){
//...
}

function rowTemplate(){
//...
}
```

```xml
<h:grid id="grid" dataSource="dataSource" resizable= "true" height= "100%" edit ="edit" rowTemplate="fn:rowTemplate"/>

```

> **提示:**
> 
> * 上述所有属性直接添加在标签内即可，（**\[\*\***属性名\]**\*\* = "..."**）,对于类型是函数的属性，需要另外在script标签中具体定义函数，这里只是一个引用,对于类型**既是函数又是字符串**类型的属性，一定要加**fn:** 来区分，加fn:的代表这是一个函数，对于**既是字符串又是布尔类型**的属性，照常输入就行了。

#### **子标签**

| 属性名 | 类型 |
| --- | --- |
| allowCopy | Object |
| columns | Array |
| columnMenu | Object |
| editable | Object |
| messages | Object |
| scrollable | Object |
| sortable | Object |
| toolbar | Array |

#### **allowCopy**子标签

| 属性 | 类型 |
| --- | --- |
| delimeter | String & Object |

```xml
<!--delimeter是Object类型-->
<h:grid>
    <h:allowCopy>
         <h:delimeter />
    </h:allowCopy>
</h:grid>

<!--delimeter是String类型-->
<h:grid>
    <h:allowCopy delimeter=",">
    </h:allowCopy>
</h:grid>
```

#### **columns**子标签

| 属性 | 类型 |
| --- | --- |
| attributes | Object |
| command | Object |
| editor | Function |
| encoded | Boolean |
| expandable | Boolean |
| field | String |
| footerTemplate | String & Function |
| format | String |
| headerAttributes | Object |
| headerTemplate | String & Function |
| minScreenWidth | Integer |
| sortable | Object |
| template | String & Function |
| title | String |
| width | Integer & String |
| hidden | Boolean |
| menu | Boolean |
| locked | Boolean |
| lockable | Boolean |
| aggregates | Array |
| columns | Object |
| filterable | Object |
| groupable | Boolean |
| groupHeaderTemplate | String & Function |
| groupFooterTemplate | String & Function |
| values | Array |
| footerAttributes | Object |

#### **columns-filterable**子标签

| 属性 | 类型 |
| --- | --- |
| cell | Object |
| multi | Boolean |
| dataSource | Function |
| checkAll | Boolean |
| itemTemplate | Function |
| operators | Object |
| search | Boolean |
| ignoreCase | Boolean |
| ui | Object |


**attributes**用法:


```xml
  <h:grid>
      <h:columns>
          <h:column>
               <h:attributes "style"="text-align:center"/>
          </h:column>
      </h:columns>
  </h:grid>
```



**command**用法：

```xml
<!--对于数组来说，有两种形式，一种是直接用逗号分隔，放在标签上的,如 aggregates;另一种是创建子标签，再创建item子标签，再将数组内的内容整合到item子标签上，如command，先创建command子标签，再创建item子标签，再将数组中的每一个集合放在item标签上-->
<h:grid>
   <h:columns aggregates="count,min,max">
      <h:column template="fn:template"/>
      <h:column field="functionId" width="30px/>
      <h:column title="列头" locked="true">
          <h:command>
             <h:item name="edit"></h:item>
             <h:item name="destroy"></h:item>
          </h:command>
      </h:column>
   </h:columns>
</h:grid>
```
**editor**用法：

```javascript
function editor(){
//...
}
```

```xml
<h:grid>
    <h:columns>
         <h:column editor="editor">
         </h:column>
    </h:coulmns>
</h:grid>
```

**filterable**用法：

```xml
<!--以filterable举例 遇到对象就向下添加子标签-->
<h:grid>
     <h:columns>
          <h:column>
              <h:filterable>
                   <h:cell dataTextField="name"/>
              </h:filterable>
          </h:column>   
     </h:columns>
</h:grid>
```

**headerAtrributes**用法:

```xml
<h:grid>
    <h:columns>
        <h:column>
             <h:headerAttributes "style"="color:red">
        </h:column>
    </h:columns>
</h:grid>
```



#### **ColumnMenu**子标签

| 属性 | 类型 |
| --- | --- |
| columns | Boolean |
| filterable | Boolean |
| sortable | Boolean |
| messages | Object |

用法:
```xml
<h:grid id="grid">
    <h:columnMenu columns="true" fileterable="false">
        <h:messages columns="choose columns"/>
    </h:columnMenu>
</h:grid >
```

#### **editable**子标签

| 属性 | 类型 |
| --- | --- |
| confirmation | Object |
| cancelDelete | String |
| confirmDelete | String |
| createAt | String |
| destroy | Boolean |
| mode | String |
| template | String & Function |
| update | Boolean |

用法:
```xml
<h:grid id="grid">
    <h:columnMenu columns="true" fileterable="false">
        <h:messages columns="choose columns"/>
    </h:columnMenu>
</h:grid >
```

#### **pageable**子标签

| 属性 | 类型 |
| --- | --- |
| pageSize | Integer |
| previousNext | Boolean |
| numeric | Boolean |
| buttonCount | Integer |
| input | Boolean |
| pageSizes | Boolean & Array |
| refresh | Boolean |
| info | Boolean |
| messages | Object |

用法:

```xml
<!--pageSizes 为array时-->
<h:grid>
   <h:pageable pageSizes="all,5,10,20,50" buttonCount="5" refresh="true">
      <h:messages display="show"/>
   </h:pageable>
</h:grid>
```

#### **messages**子标签

| 属性 | 类型 |
| --- | --- |
| commands | Object |
| noRecords | String |

用法:
```xml
<h:grid>
   <h:messages noRecords="no data">
      <h:commands cancel="cancel"/>
   </h:messages >
</h:grid>
```

#### **noRecords**子标签

| 属性 | 类型 |
| --- | --- |
| template | String & Function |

用法:

```xml
<h:grid>
    <h:noRecords template="no records">
    </h:noRecords>
</h:grid>
```

#### **scrollable**子标签

| 属性 | 类型 |
| --- | --- |
| virtual | Boolean |

用法:

```xml
<h:grid>
    <h:scrollable virtual="true"/>
</h:grid>
```


#### **sortable**子标签

| 属性 | 类型 |
| --- | --- |
| allowUnsort | Boolean |
| mode | String |

用法:

```xml
<h:grid>
   <h:sortable allowUnsort="true" mode="multiple"/>
</h:grid>
```

#### **toolbar**子标签

| 属性 | 类型 |
| --- | --- |
| name | String |
| template | String & Function |
| text | String |

```javascript
function template(){
//...
}
```

```xml
<!--toolbar是数组 所以属性都是针对于数组内的集合来说的-->
<h:grid>
   <h:toolbar>
      <h:item name="create">
            <!--当template为String时，由于xml会对标签的封闭符号敏感，所以可以采取下面的方式-->
            <h:template>
              <![CDATA[<span class=" btn btn-primary k-grid-add">#=text#</span>]]>
            </h:template>
         </h:item>
   </h:toolbar>
</h:grid>
```

> **提示**：
> 
> * 遇到**object**类型 就向下添加子标签，当遇到普通类型时，就添加到当前的标签上,就像处理一般属性那样。

