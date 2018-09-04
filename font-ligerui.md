
# 前端开发手册 (1.0)

#### 目录划分

按照web目录规范，前端文件存放在如下目录
```
  webapp
    ├─lib
    │  ├─images
    │  ├─jquery
    │  ├─jquery-validation
    │  └─ligerUI
    ├─resources
    │  ├─css
    │  ├─font
    │  ├─images
    │  ├─js
    └─WEB-INF
        ├─spring
        └─view

```
资源文件目录
资源文件按照类型划分为两个目录：**`lib`**和**`resources`**

 - **`lib`**目录存放LigerUI的所有文件
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

例如：用户管理界面中用户查询的form对象
>var d_um_002_form = $('#d_um_002_form').ligerForm({...})

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
前端界面采用LigerUI开源框架，相同的显示效果可以采用多种代码方式来实现，为了规范项目组成员的代码样式，统一编码风格，避免以后维护的工作量，根据不同的应用场景总结以下几种开发实践.

通用引入头文件

界面中会引入一些资源文件，例如脚本，样式文件等。为了避免每个界面都重复引入增加工作量以及后续维护的困难，每个界面应该引入通用的文件头。
```html
<#include "../../include/head.html">
```

界面多语言实现

界面中的多语言在数据库中统一存放在sys_prompts表中，系统启动时加载进redis缓存。界面中实现多语言是通过freemarker的标签以及对应的多语言code来实现。
例如：“确定”的多语言code是“sys.hand.btn.ok”在界面中的写法：
```html
<@spring.message "sys.hand.btn.ok"/>
```

表单(form)实现

通过脚本方式来创建form对象，避免直接写html的方式创建.通过脚本方式更灵活，也更好控制

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

一般的查询界面都会包含一个查询的表单，以及一个数据展示的grid，查询按钮函数中调用ajax把表单中的查询条件提交到后台返回json数据。这里可以直接调用Hap.gridQuery函数将form对象以及grid作为参数传递进去。
具体Hap.gridQuery查询参数参考 Hap.js中的注释。



表单校验

表单验证采用的是jQuery validation库，因此表单验证写法一致。
在field上定义validate对象
```javascript
{
    display : "用户名",
    name : "userName",
    type : "text",
    validate : {
        required : true,
        minlength : 10
    }
}
```
对于复杂的校验逻辑可单独写一个脚本函数，在提交数据前调用判断。

表单加载数据

加载form数据可以通过调用Hap.loadForm函数
```javascript
Hap.loadForm({
    form: form,
    url: '${base.contextPath}/sys/code/query',
    para: {}
})
```

表格Grid

 - 定义grid的dom容器
 
 ```html
 <div id="d_um_002_grid" style="margin: 0; padding: 0"></div>
 ```
 - 根据id生成grid对象
 
 ```javascript
 window['d_um_002_grid'] = $("#d_um_002_grid").ligerGrid({})
 ```

Grid工具栏

 - 新增按钮
 
```javascript
{ 
	  text: '<@spring.message "sys.hand.btn.new"/>', 
      click: function(){
        d_um_002_grid.addRow({})
      }, 
      icon: 'add' 
}
```
 - 删除按钮
 
 ```javascript
 { 
   text: '<@spring.message "sys.hand.btn.delete"/>', 
   click: function(){
       Hap.gridDelete({
           grid:d_um_002_grid, 
           url:'${base.contextPath}/sys/user/remove'
       }) 
   }
}
 ```

Hap.gridDelete函数具体参数如下:
```javascript
/**
 * 表格删除函数代理. 
 * <ul>
 * <li>options.grid: grid对象</li>
 * <li>options.confirmTip: 是否删除提示信息</li>
 * <li>options.url: 提交删除的url</li>
 * <li>options.waitingTip: 等待提示信息</li>
 * <li>options.successTip: 删除成功提示信息</li>
 * <li>options.infoTip:提示标题信息</li>
 * </ul>
 * @param {options}  删除配置选项
 * /
```
 * 保存函数
 
 ```javascript
 { 
    text: '<@spring.message "sys.hand.btn.save"/>', 
    click: function(){
        Hap.gridSave({
            grid:d_um_002_grid,
            url:'${base.contextPath}/sys/user/submit'
         }) 
      }, 
     icon: 'save'}

 ```
**注意: 为了客户端不再重新刷新，服务端一定要返回提交的数据** 
```java
return new ResponseData(users);
```



Hap.gridSave函数可对新增以及修改过的数据统一提交到服务端.
gridSave函数的具体参数如下:
```javascript
/**
 * 表格保存函数代理.
 * <ul>
 * <li>options.grid: grid对象</li>
 * <li>options.url: 保存的url</li>
 * <li>options.waitingTip: 等待提示信息</li>
 * <li>options.successTip: 删除成功提示信息</li>
 * <li>options.infoTip:提示标题信息</li>
 * </ul>
 * @param {options} 删除配置选项
 */
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
root: 'rows',//数据集字段
record: 'total'//总记录数字段
```

头行关系

头行数据建议采用form+grid的形式.其中form展现头信息，grid展现行信息。
form和grid的定义参考以上章节，这里主要介绍保存逻辑
```javascript
function f_save() {
            Hap.submitForm({
                form:form,
                grid:codeValueGrid,
                gridName:'codeValues',
                url:'${base.contextPath}/sys/code/submit',
                success:function(json,opt){
                    $.ligerDialog.success(
         '<@spring.message "sys.hand.tip.success"/>', 
         '<@spring.message "sys.hand.tip.info"/>', 
          function(){
                        parent.d_sc_002_grid.reload();
                        f_cancel();
                    });
                    
                }
            });
} 
```
>头行保存这里调用了Hap.submitForm方法，参数对象中包含了form对象以及grid对象，gridName定义行数据存放在头上的属性名。

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
 -  **`延迟加载`** grid数据是否自动加载
 -  **`查询条件必输`** 是否必须输入查询条件
 
 grid字段说明
 
 - **`显示`** 字段的描述(支持多语言)
 - **`字段名`** 字段的name
 - **`列宽度`** 字段的宽度
 - - **`表格列`** 是否是表格字段
 - **`查询字段`** 是否是查询字段
 - **`列序号`** 表格列排序号
 -  **`查询配置`** 如果“查询字段”为“是”，配置查询字段类型
 - **`自动完成`** 是否是自动完成列(每个表格列只能有唯一一个自动完成列)


点击"查询配置"，配置查询字段

![enter image description here](http://7xruj6.com
1.z0.glb.clouddn.com/sys_lov_select.png)

![enter image description here](http://7xruj6.com1.z0.glb.clouddn.com/sys_lov_lov.png)
查询字段可以配置多种类型，文本，数字，下拉框以及值列表

 - **`字段类型`** 查询字段的类型
 - **`新行`** 是否新开一行显示
 - **`查询字段宽度`** 查询字段宽度
 - **`查询字段序号`** 查询字段排序号
 - **`查询字段名`** 查询字段名
 - **`快速编码`** 当“字段类型”为“下拉框”时指定快速编码的code
 - **`通用lov编码`** 当“字段类型”为“值列表”时指定通用lov编码

定义好lov配置后，在html中通过以下代码生成lov
`${lovService.getLov(contextPath,locale,code)}`

grid中
```javascript
{ 
	display : '通用lov1',
	name : 'roleId',
	width : 150,
	textField:'roleName',
	align: 'left',
	editor: ${lovService.getLov(base.contextPath,base.locale, "lov_role")} 
}
```
form中
```javascript
{
	display : "通用lov(角色)",
	name : "roleId",
	type : "popup",
	newline : false,
	textField:'roleName',
	group : "基本信息",
	options:${lovService.getLov(base.contextPath,base.locale, "lov_role")}
}
```
###多语言编辑器###
框架中提供了TLEdit编辑器来统一多语言设置。
首先更新到最新的框架版本.


form中使用TLEdit
```javascript
{
    display: '<@spring.message "code.description"/>',
    name: 'description',
    type: 'tl',
    options: {
        idField: 'codeId',
        dto: 'com.hand.hap.sys.dto.Code'
    }
}
```


grid中使用TLEdit
```javascript
{
    display: '<@spring.message "codevalue.description"/>',
    name: 'description',
    width: 200,
    align: 'left',
    editor: {
        type: 'tl',
        idField: 'codeValueId',
        dto: 'com.hand.hap.sys.dto.CodeValue'
    }
}
```

字段说明 ：
 - **`name`** 多语言字段名
 - **`type`** 多语言编辑器'tl'
 - **`idField`** dto中主键字段名（确保当前form中有对应的值)
 - **`dto`** dto的类名
