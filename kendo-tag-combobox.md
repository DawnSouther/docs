
# Combobox标签

ComboBox下拉列表组件

```xml
 <h:combobox placeholder="combobox" 
             valuePrimitive="true" 
             dataTextField="text"
             dataValueField="value" 
             bind="enabled: isEnabled, source: comboboxSource, value:data.productId" 
             style="width: 100%;"/>
```

### **主要属性**
属性名    | 类型
-------- | ---
animation | Boolean
autoBind | Boolean
autoWidth | Boolean
cascadeFrom| String
cascadeFromField | String
clearButton| Boolean 
dataSource | DataSource
dataTextField | String
dataValueField| String
delay | Number
enable | Boolean
enforceMinLength| Boolean
filter | String
fixedGroupTemplate| String \| Function
footerTemplate| String \| Function
groupTemplate| String \| Function
height| Number
highlightFirst| Boolean
ignoreCase| Boolean
index| Number
minLength| Number
noDataTemplate| String \| Function
placeholder | String
suggest| Boolean
headerTemplate| String \| Function
template| String \| Function
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
> 属性用法请参考KendoUI文档 [http://docs.telerik.com/kendo-ui/api/javascript/ui/combobox](http://docs.telerik.com/kendo-ui/api/javascript/ui/combobox) 


### **用法示例**

```javascript
//定义数据源DataSource
var dataSource= new kendo.data.DataSource({
    data:["one","two"]
});

//下拉列表渲染函数
var itemTemplate = function (){
    //...
}

//选中某一个列表选项后触发
function onComboboxChange(e){
    var value = this.value();
    // Use the value of the widget.
}
//以上两种函数定义方法皆可
```

```xml
<h:combobox id="combobox" dataSource="dataSource" clearButton="true" placeholder="test" enable="false" template="fn:itemTemplate" change="onComboboxChange"/>
```
> **提示:** 某些属性类型为**String \| Function** 时，表明它可以定义为字符串或者函数。因此当需要定义为Function时需要增加前缀**fn:**来区分(例如函数template="fn:itemTemplate" )

### **主要子节点**
* animation
* popup
* virtual

### **animation**
主要用来定义动画效果，例如打开关闭效果

* open 打开效果 
* close 关闭效果

属性| 类型 
------ |----
effects| String 
duration| Number

```xml
<h:combobox id="combobox">
    <h:animation>
       <h:close effects="zoom:out" duration="300"/>
       <h:open effects="zoom:in" duration="300"/>
    </h:animation>
</h:combobox>
```
### **popup**
弹出窗口设置

属性| 类型
--------|------
appendTo| String
origin  | String
position| String

```xml
<h:combobox id="combobox">
    <h:popup appendTo="container" origin="top left" position="top left"/>
</h:combobox>
```

### **virtual**
动态加载相关设置

属性    | 类型
-----------|-----
itemHeight | Number
mapValueTo | String
valueMapper| Function


```javascript
function valueMapper(){
    $.ajax({
        url: "http://demos.telerik.com/kendo-ui/service/Orders/ValueMapper",
        type: "GET",
        dataType: "jsonp",
        data: convertValues(options.value),
        success: function(data) {
            //the **data** is either index or array of indices.
            //Example:
            // 10258 -> 10 (index in the Orders collection)
            // [10258, 10261] -> [10, 14] (indices in the Orders collection)
            options.success(data);
        }
    })
}
```

```xml
<h:combobox id="combobox">
    <h:virtual itemHeight ="20" mapValueTo="index" valueMapper="valueMapper"/>
</h:combobox>
```
