Kendo UI Grid
----------------------

#### **简介**：kendoui表格的基本格式
Kendo UI Grid为开发者提供了丰富的功能，从基本的筛选、排序，到高级的编页、分层数据分组等。
如下所示，一个简单的demo，columns相当于表格的tr ，每一个{...}就相当于每一行，由于kendoGrid是动态生成的，所以拥有一个dataSource属性，用来读取数据源，


```javascript
$("#grid").kendoGrid({
  columns: [{
    field: "name",
    title: "Name" 
  }, {
    field: "age",
    title: "Age" 
  }],
  dataSource: [ { name: "Jane", age: 30 }, { name: "John", age: 33 }]
});
```

#### **具体介绍**
1. columns 

  >**columns中我们常用的属性有:field,title,width,template等，当然还有更多属性，在这里不一一介绍，详情请查询官方API**

  > - field:字段名，必须与请求的数据里的key保持一致，这样才能直接读取key里面的value值。并且同时便于数据的保存
  > -  title:等同于表格的th,没有特殊限制
  > - width:表格列的宽度
  > - template:表格模板，Kendo UI Grid使开发者可以自己定义表格列的样式



```
 //自定义一个checkbox宽度为20的列
 columns: [
      {
         template: '<input type="checkbox"/>',
         width: 20
      },
```

2.dataSource
dataSource本来就是一个单独的组件，所以它可以有两种表达，一种是定义在kendoGrid方法体内的，另一种是抽象出去，形成一个独立的dataSource

```
$("#grid").kendoGrid({
  columns: [
    { field: "name" },
    { field: "age" }
  ],
  dataSource: [
    { name: "Jane Doe", age: 30 },
    { name: "John Doe", age: 33 }
  ]
});
```

```javascipt
var dataSource = new kendo.data.DataSource({
  transport: {
    read: { //从url路径请求数据  也就是查询数据
      url: "http://demos.telerik.com/kendo-ui/service/products",
      dataType: "jsonp"
    },
    create: {//新建
       url: "...",
       contentType: "application/json",//定义数据类型
       type: "POST"//定义请求方式
    },
    update: {//修改
       url: "...",
       contentType: "application/json",
       type: "POST"
    },
    destroy: {//删除
       url: "...",
       contentType: "application/json",
       type: "POST"
    },
  },
  pageSize: 10 //每一页10条数据
});
$("#grid").kendoGrid({
  dataSource: dataSource,
  pageable: true //是否分页
});
```
>**dataSource的用法:**

> - kendoUI提供可以向服务端发送请求的方法 
> - kendoUI中dataSource具体用法请参考: http://docs.telerik.com/kendo-ui/api/javascript/data/datasource


3.分页
针对于大量数据，kendoUIGrid提供分页查询，使用起来非常便捷

```
//重点还是在服务器端的分页，即dataSource内决定
//dataSource
serverPaging: true,//是否在服务器端分页
pageSize    : 10//初始是一页多少条数据
//Grid
pageable: {
     pageSizes:[5, 10, 20, 50],//每页可以有多少数据
     refresh:true,//是否可以刷新
     buttonCount:5 //超过几页以上出现折叠
  },
```

如需其他需要，请参考:http://docs.telerik.com/kendo-ui/api/javascript/ui/grid#configuration-pageable


#### **使用实例**
1.全选
参考demo:
http://docs.telerik.com/kendo-ui/controls/data-management/grid/how-to/Templates/grid-with-checkbox-column
http://docs.telerik.com/kendo-ui/controls/data-management/grid/how-to/Editing/delete-multiple-rows-with-checkboxes

```
//1 columns中添加一行checkbox复选框
{
//头模板 相当于title的位置 放置一个用来全选的复选框 
 headerTemplate: "<input id='headCheckbox' type='checkbox' onclick='toggleAll(event)' tabindex='-1'/>",
 //绑定checked事件
 template: '<input class="checkbox hCheckbox" type="checkbox" data-bind="checked: checked" tabindex="-1"/>',
 width: 20
},

//2 为Grid添加dataBound事件
dataBound: function () {
    //当前分页里的所有内容
   var view = this.dataSource.view();
   this.items().each(function (index, row) {
    //将一行数据都绑定在view上，这样选中的就是一行数据了
     kendo.bind(row, view[index]);
   });
}

//3 定义全选按钮
function toggleAll(e) {
        var view = dataSource.view();
        //在步骤一中绑定了checked事件的复选框 在这里会被选到
        var checked = e.target.checked;
        for (var i = 0; i < view.length; i++) {
            //让所有的复选框checked属性变成选中状态
            view[i].set("checked", checked);
        }
}
```

2.编辑器的实现
当表格编辑时，想要从下拉框中选择内容，或者想要从lov中选中内容，或者定义复选框，其实都是在模板中定义样式
combobox

```
//参考sys_lov_edit.html
{
    field: "gridFieldAlign",
    title: '...',
    width: 80,
    headerAttributes: {//定义表头样式 html样式
         "class": "table-header-cell",
         style: "text-align: center"
    },
    template: function (dataItem) {//模板函数
       //将field的数据和下拉框内的数据进行绑定
        var v = dataItem.gridFieldAlign;
        //alignType定义好的一个数据源，想当于dataSource
        $.each(alignType, function (i, n) {
             if ((n.value || '').toLowerCase() == (v || '').toLowerCase())
             {
                 v = n.meaning;
                 return false;
              }
         })
       return v || '';
    },
    editor: function (container, options) {
    //编辑时出现下拉框,options.field就是当前选中的field
        $('<input name="' + options.field + '"/>').appendTo(container).kendoDropDownList(
         {
         //下拉框alignType其实也是json字符串，同样是key-value
         dataTextField放置显示的value，dataValueField放置key
             dataTextField: "meaning",
             dataValueField: "value",
             valuePrimitive: true,//如果不是true的话，选中内容会是一个object 并不是一个简单的json字符串
             dataSource: alignType
         });
     }
 },
```

lov

```
//参考sys_function.html
{
    field: "resourceId",
    title: '<@spring.message "function.mainpage"/>',
    width: 120,
    template: function (dataItem) {
      return dataItem['resourceName'] || ''//如果不加，此行会根据field添写字段
    },
    editor : function (container, options) {
    //LOV_RESOURCE为在lov定义中自定义的lov
      $('<input name="' + options.field + '"/>').appendTo(container).kendoLov($.extend(<@lov "LOV_RESOURCE"/>, 
      {
          textField: 'resourceName',//显示的字段
          model    : options.model//将lov选中数据放在model中
       }));
     }
}
```

2.按钮
对于表格的增加，保存，取消，kendoUIGrid提供了自己定义好的按钮，详情可参考：
http://docs.telerik.com/kendo-ui/api/javascript/ui/grid#configuration-toolbar
kendoUIGrid也允许自定义按钮，下面的例子实现了重新定义的增删保存取消按钮

增加保存取消

```javascript
  //viewModel
  createFunction: function(){
      //调用增加一行的方法，在表格内增加一行
      $('#grid').data('kendoGrid').addRow();
  },
  saveFunction: function(){
      //调用保存方法，使用grid绑定的dataSource中的保存方法
      $('#grid').data('kendoGrid').saveChanges();
  },
  cancelFunction: function(e){
      //取消更改，调用kendoUI自带的cancelChanges()方法
      $('#grid').data('kendoGrid').cancelChanges();
  }
```

```html
//在html中添加标签
//新增按钮
<div id="tool-bar">
<span class="btn btn-primary k-grid-add" data-bind="click:createFunction"><@spring.message "hap.new"/></span>
 //保存按钮
<span class="btn btn-success k-grid-save-changes"   data-bind="click:saveFunction" ><@spring.message "hap.save"/></span>
 //取消按钮   
<span  class="btn btn-default k-grid-cancel-changes" data-bind="click:cancelFunction"><@spring.message "hap.cancel"/></span>
</div>
//绑定viewModel kendo.bind("#tool-bar",viewModel)
```

删除

```html
<span  onclick="deleteData()" class="btn btn-danger"></span>
```

```javascript
//参考sys_function.html 根据复选框选中内容进行批量删除
function deleteData() {
        //得到选中内容
        var checked = $("#grid").find(".hCheckbox:checked")        if (checked.length) {//如果选中
          // kendo.ui.showConfirmDialog  kendoUI自带提示框
           kendo.ui.showConfirmDialog({
              title:$l('hap.tip.info'),
              message: $l('hap.tip.delete_confirm')
           }).done(function (event) {
           //当点击确认时会返回OK 取消时关闭提示框
            if (event.button == 'OK') {
                var data = [];
                //将选中内容放入data中
                checked.each(function () {                                                   data.push(grid.dataItem($(this.closest("tr"))));
                })
                //删除每一条数据
                for (var i = 0; i < data.length; i++) {
                //remove调用dataSource中的destroy方法
                    grid.dataSource.remove(data[i])
                }
                //同步grid
                grid.dataSource.sync();
            }
        })
       }
    }
```

对于kendoGrid的更多用法属性，具体请参考http://docs.telerik.com/kendo-ui/api/javascript/ui/grid