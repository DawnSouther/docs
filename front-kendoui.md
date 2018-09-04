# 前端开发手册 (2.0)

#### 目录划分

按照web目录规范，前端文件存放在如下目录
```
  webapp
    ├─lib
    │  ├─bootstrap-3.3.7
    │  ├─font-awesome-4.6.3
    │  └─kendoui
    ├─resources
    │  ├─css
    │  ├─font
    │  ├─images
    │  ├─js
    │  ├─upload
    └─WEB-INF
        ├─web.xml
        └─view

```
资源文件目录
资源文件按照类型划分为两个目录：**`lib`**和**`resources`**

 - **`lib`**目录存放kendoui的所有文件
 - **`resources`**目录存放程序通用的资源文件

功能文件目录
功能文件存放在**`view`**目录下，目录的命名规则按照
> **[模块代码]**/**[功能项代码]**

例如：用户管理的功能代码是SYS001,那么用户管理相关的界面文件都存放在
```
src/main/webapp/WEB-INF/view/sys/sys001/
```
#### 文件命名约定
界面文件按照如下格式：
>[模块代码]<u> </u>[业务对象]<u> </u>[功能操作].html

例如：用户查询界面: sys_user_query.html

####url命名约定
对于系统中提交的url地址，按照以下格式命名
>[contextPath]/[模块代码]/[业务对象]/[操作类型]

对于常见的几种操作类型定义如下

查询URL
>[模块代码]/[业务对象]/query

批量提交  
>[模块代码]/[业务对象]/submit

批量删除
> [模块代码]/[业务对象]/remove

例如用户的查询: sys/user/query

####代码编程规范

javascript注释
文件头上定义版权声明以及文件的主要描述信息
```javascript
/**
 * @summary Hap
 * @description 抽象通用函数
 * @version 1.0
 * @author njq.niu@hand-china.com
 * @copyright Copyright Hand China Co.,Ltd.
 */
```
函数上注释
```javascript
/**
 * 提交表单数据(头行数据可一起提交).
 *
 * <ul>
 * <li>options.form: form对象</li>
 * <li>options.grid: grid对象</li>
 * <li>options.gridName: grid数据的name</li>
 * <li>options.url: 提交的url</li>
 * <li>options.success: success回调函数</li>
 * <li>options.failure: failure回调函数</li>
 * </ul>
 * @param {options}
 */

```

freemarker 注释
端界面主要采用freemarker模板技术，文件后缀名统一为.html
```html
<#--
 * Description: 快速编码修改界面
 * Version: 1.0
 * Author: njq.niu@hand-china.com
 * Copyright Hand China Co.,Ltd.
-->
```
css注释
```css
/*!
 * description:通用样式文件
 * version: 1.0
 * author: njq.niu@hand-china.com
 * Copyright Hand China Co.,Ltd.
 */

```

脚本变量
变量命名原则是减少变量冲突，采用Camel命名法。
>var [开发项ID]_[具体业务含义] = {}

例如：用户管理界面中用户查询的grid数据
>var d_um_002_grid = $('#d_um_002_grid').data("kendoGrid");

脚本函数名
函数命名也采用Camel命名法.
>function [开发项ID]_[具体业务含义](){...}

例如用户查询函数
```javascript
function d_um_002_query(){
    ....
}
```

####开发实践
前端界面采用kendoUI开源框架，相同的显示效果可以采用多种代码方式来实现，为了规范项目组成员的代码样式，统一编码风格，避免以后维护的工作量，根据不同的应用场景总结以下几种开发实践.

通用引入头文件

界面中会引入一些资源文件，例如脚本，样式文件等。为了避免每个界面都重复引入增加工作量以及后续维护的困难，每个界面应该引入通用的文件头。
```html
<#include "../include/header.html">
```

界面多语言实现

界面中的多语言在数据库中统一存放在sys_prompts表中，系统启动时加载进redis缓存。界面中实现多语言是通过freemarker的标签以及对应的多语言code来实现。
例如：“确定”的多语言code是“sys.hand.btn.ok”在界面中的写法：
```html
<@spring.message "sys.hand.btn.ok"/>
```

表单(form)实现

表单主要采用bootstrap的栅格来布局

 * 第一步首先创建一个form的容器对象，一般我们采用div

 ```html
 <div id="d_um_002_form" ></div>
 ```
 * 在脚本中根据容器id创建form对象
```javascript
 window['d_um_002_form'] = $("#d_um_002_form").ligerForm({
    fields: [{
        type: 'text',
        label: 'loginname',
        name: 'loginName'
    }, {
        type: 'text',
        label: 'name',
        newline: false,
        name: 'name'
    }],
    buttons: [{
        text: '查询',
        width: 60,
        click: function() {
            Hap.gridQuery({
                form: d_um_002_form,
                grid: d_um_002_grid
            })
        }
    }]
});
```

一般的查询界面都会包含一个查询的表单，以及一个数据展示的grid，查询按钮函数中调用kendoUIGrid的datasource中的read方法去提交查询数据。这里可以直接使用$('#grid').data('kendoGrid').dataSource.page(1);来调用查询方法,查询表单数据一般需要绑定在viewModel中,在查询数据时，将viewModel中的值传递到后台去

 * viewModel绑定表单数据

1.定义viewModel

```
var viewModel = kendo.observable({
               model : {}
}
```
   2.定义input标签并绑定viewModel

```
<input type="text" id="promptCode" data-bind="value:model.promptCode">
<script>kendo.bind($('promptCode'), viewModel);</script>
```
  3.查询时传递参数

```
parameterMap : function(options) {
    var map = viewModel.model.toJSON();
    map.page = options.page;
    map.pagesize = options.pageSize;
    return map;
}
```

表单校验

表单验证采用的是kendoUI提供的kendoValidator组件来验证

```html
<input id="password" type="password"/>
```

```javascript
var validator = $("#password").kendoValidator({
    messages: {
        pwdlen:'<@spring.message "user.password.length"/>'
    },
    rules: {//自定义规则
        pwdlen: function(input){
        {
            if(input.val().length < 10){
                return false;
        }
        return true;
     }
});
```
对于简单的校验逻辑可直接定义一个验证错误的模板，如必需字段，无需在定义规则。

表单加载数据

加载form数据可以通过页面ajax请求,也可以通过后台传参，在viewModel.model里绑定，可直接加载到表单上

```javascript
model:{
   length: "${length}",
   complexity:"${complexity}"
}
```

表格Grid

 - 定义grid的dom容器

 ```html
 <div style="clear:both">
        <div id="grid"></div>
 </div>
 ```
 - 根据id生成grid对象

 ```javascript
$("#grid").kendoGrid({
    dataSource:dataSource,
    columns : [{
                field : "promptCode",
                title : '<@spring.message "prompt.promptcode"/>',
                width : 240,
                headerAttributes: {
                    "class": "table-header-cell",
                    style  : "text-align: center"
                },
                attributes : {
                    //输入只能小写
                    style : "text-transform:lowercase"
                }
        },
        ...
        ]
});
 ```

Grid工具栏

 - 新增按钮

```javascript
toolbar    : [{
     name: "create",
     template : '<span class="btn btn-primary k-grid-add"><@spring.message "hap.new"/></span>'//自定义模板
 }
```
 - 删除按钮(可在表单自定义按钮)

 ```html
<span onclick="deleteData()" class="btn btn-danger"><@spring.message "hap.delete"/></span>
 ```

```javascript
function deleteData() {
    var checked = grid.selectedDataItems();
    if(grid.selectedDataItems().length){  
         kendo.ui.showConfirmDialog({
             title:$l('hap.tip.info'),
             message: $l('hap.tip.delete_confirm')
         }).done(function (event) {
             if (event.button == 'OK') {
                $.each(checked,function(i,v){
                    grid.dataSource.remove(v)
                })
                grid.dataSource.sync();
             }
         })
    }
}
```

 * 保存函数(绑定viewModel方法)
 ```html
 <span class="btn btn-success k-grid-save-changes" data-bind="click:saveFunction"><@spring.message "hap.save"/></span>

 ```
 ```javascript
var viewModel = kendo.observable({
        model: {},
        saveFunction: function () {
             $('#grid').data('kendoGrid').saveChanges();
        },
        queryResource: function (e) {
             $('#grid').data('kendoGrid').dataSource.page(1);
        }
 });
 ```
**注意: 为了客户端不再重新刷新，服务端一定要返回提交的数据**
```java
return new ResponseData(users);
```

JSON数据格式

grid和服务端的数据交互统一采用json格式
```javascript
{
        success:true,
        rows[{},{}],
        total:10
}
```
success: ajax是否成功的标志 true|false
rows: 返回的数据集
total: 总记录数

需要在grid中定义json的格式属性
```javascript
pageSize: 10, //分页大小
serverPaging: true,//服务器端是否分页
```

头行关系

头行数据建议采用form+grid的形式.其中form展现头信息，grid展现行信息。
form和grid的定义参考以上章节，这里主要介绍保存逻辑
```javascript
 Hap.submitForm({
       url: '${base.contextPath}/sys/lov/submit',
       formModel: viewModel.model,
       grid: {
             lovItems: $('#grid')
       },
       success: function (result) {
            if (!lovId) {
                 lovId = viewModel.model.lovId
            }
       }
});
```
>头行保存这里调用了Hap.submitForm方法，参数对象中包含了form对象以及grid对象，gridName定义行数据存放在头上的属性名。

```javascript
/**
 * 提交表单数据(头行数据可一起提交).
 *
 * <ul>
 * <li>options.formModel: form对象即viewModel.model</li>
 * <li>options.grid: grid对象</li>
 * <li>options.gridName: grid数据的name</li>
 * <li>options.url: 提交的url</li>
 * <li>options.success: success回调函数</li>
 * <li>options.failure: failure回调函数</li>
 * </ul>
 * @param {options}
 */
```

通用lov设置

系统中可以设置一些常用的lov方便其他模块调用。
进入系统功能->lov定义

点击新增按钮，创建一个lov
![enter image description here](http://7xruj6.com1.z0.glb.clouddn.com/sys_lov_edit.png)

form字段说明：

 - **`代码`**  lov的编码，全局唯一，通过此code获取lov
 - **`描述`**   lov的描述字段，仅用于说明
 - **`sqlid`** lov查询的sqlid，对应后台xml中sql的id
 - **`标题`** 弹出lov的标题
 - **`valueField`** popup中的valueField字段
 - **`textField`** popup中的textField字段
 - **`宽度`** lov的宽度
 -  **`高度`** lov的高度

 grid字段说明

 - **`显示`** 字段的描述(支持多语言)
 - **`字段名`** 字段的name
 - **`列宽度`** 字段的宽度
 - - **`表格列`** 是否是表格字段
 - **`查询字段`** 是否是查询字段
 - **`列序号`** 表格列排序号
 -  **`查询配置`** 如果“查询字段”为“是”，配置查询字段类型

点击"查询配置"，配置查询字段

![enter image description here](http://7xruj6.com1.z0.glb.clouddn.com/sys_lov_select.png)

![enter image description here](http://7xruj6.com1.z0.glb.clouddn.com/sys_lov_lov.png)
查询字段可以配置两种类型，文本和下拉框

 - **`字段类型`** 查询字段的类型
 - **`查询字段宽度`** 查询字段宽度
 - **`查询字段序号`** 查询字段排序号
 - **`查询字段名`** 查询字段名
 - **`快速编码`** 当“字段类型”为“下拉框”时指定快速编码的code
 - **`url`** 当“字段类型”为“下拉框”时指定url的url,textFiled,valueFileds

定义好lov配置后，在gird和form中通过以下代码使用lov

grid中
```javascript
{
   field: "parentPositionId",
   title: '<@spring.message "position.parentpositionname"/>',
   width: 140,
   template  : function (dataItem) {
       return dataItem['parentPositionName'] || '';
   },
   editor  : function (container, options) {
      $('<input name="' + options.field + '"/>')
                            .appendTo(container)
                            .kendoLov($.extend(<@lov "LOV_POSITION"/>, {
          textField: 'parentPositionName',
          query:function(e){
              //e.param['positionId'] = options.model.positionId;
          },
          model    : options.model
       }));
    }
 }
```
form中
```html
<input type="text" id="role" data-bind="value:model.roleId,text:model.roleName">
```
```javascript

<script>     $("#role").kendoLov(<@lov "LOV_ROLE"/>)  
</script>
```

字段说明 ：
 - **`textField`** 字段名
 - **`model`** viewModel中的mdel,在grid时，是当前操作绑定的model
 - **`query`** 向lov传递参数
 - **`select`**使用和query相同，在lov选中时触发
 - **`dto`** dto的类名

###多语言编辑器###
框架中提供了TLEdit编辑器来统一多语言设置。
首先更新到最新的框架版本.


form中使用TLEdit
```html
<input id="name" type="text" data-bind="value:model.name">
```
```javascript
<script>
    $("#name").kendoTLEdit({
        idField: 'unitId',
        field: 'name',
        dto: "com.hand.hap.hr.dto.HrOrgUnit",
        model: newViewModel.model
       })
     kendo.bind($('#name'), newViewModel);
</script>
```


grid中使用TLEdit
```javascript
 {
     field : "functionName",
     title : '<@spring.message "function.functionname"/>',
     width: 120,
     editor: function (container, options) {
        $('<input name="' + options.field + '"/>').appendTo(container).kendoTLEdit({
              idField: 'functionId',
              field: 'functionName',
              dto: "com.hand.hap.function.dto.Function",
              model: options.model
         });
      }
}
```

字段说明 ：
 - **`field`** 多语言字段名
 - **`model`** viewModel中的mdel,在grid时，是当前操作绑定的model
 - **`idField`** dto中主键字段名（确保当前form中有对应的值)
 - **`dto`** dto的类名
