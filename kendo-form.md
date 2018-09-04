Form表单
-----------------
页面表单部分

>**kendoui的表单部分常用的有**

> - textbox
> - combobox
> - lov
> -  datapicker
>**当组件被加载时，首先通过data-role去判断当前组件是什么，然后在根据data-bind去绑定资源**
#### <i class="icon-file"></i>textbox

```html
//1.
 <input type="text" placeholder='...' data-bind="value:model.url" class="k-textbox">

//2.
<div class="form-group">
    <label class="col-sm-2 control-label">...</label>
    <div class="col-sm-10">
        <input type="text" class="k-textbox" data-bind="value:model.placeholder">                                   
     </div>
</div>
```
> **注意:**

> - kendoui中textbox的样式：.k-textbox
> data-role = "maskedtextbox",如果data-role不存在，会默认为文本框


#### <i class="icon-file"></i>combobox
在下拉框原有定义上，加入了data-role属性，使kendoui通过data-role寻找到的组件特性加载它，并且同时根据data-bind里面的source属性加载数据源，也就是下拉框的内容

```html
<select data-role="combobox" data-value-primitive="true" placeholder='...'  data-text-field="meaning" data-value-field="value" data-bind="source: resourceTypeData2, value: model.type">
</select>

```
> **注意:**

> - data-bind中的source指定了下拉框的**数据来源**,
> - data-text-field 指定下拉框显示字段
> - data-value-field 指定下拉框显示属性字段名
>  - data-value-primitive 绑定会是一条数据，而不是一个对象


#### <i class="icon-file"></i>lov
kendoui并没有实现lov，所以没有data-role

```html
<input type="text" id="role" placeholder='...'  data-bind="value:model.roleId,text:model.roleName">
  <script>
  $("#role").kendoLov(${lovProvider.getLov(base.contextPath, base.locale, "LOV_ROLE")})  
 </script>
```
> **注意:**

> - LOV_ROLE是在lov定义中自定义的lov
> - data-bind中text 是值从lov中选中数据后会在输入框中显示的字段

#### <i class="icon-file"></i>datapicker
时间选择器，会弹出一个日历让你选择

```html
<input id="startTime" data-bind="value:model.start">
<script>
$("#startTime").kendoDatePicker({
			    animation: {
			    	close: {
					},
				    open: {
				    }
			    },
			    format: "{0:yyyy-MM-dd}",
			    change: function(){			    	
			    }
			});
</script>
```

> **注意:**

> - 日期选择器kendoDatePicker中有多种方法属性，例如change:当日期发生变化时触发的函数，format格式属性等等

-----------------
#### **表单回车查询**

```javascript
$('#query-form input').keydown(function (e) {
	    if (e.keyCode == 13) {
	        e.target.blur();
	        viewModel.queryResource(e);
	    }
});
```

>**详解：**

> - query-form 是表单id，input指表单内所有输入框
> - viewModel.queryResource(e)当按下回车按钮时都执行查询方法

---------------

#### <i class="icon-file"></i> 表单校验
1.前端校验

```html
<input id="priorityLevel" name="priorityLevel" required 
  data-bind="value: model.priorityLevel">
<script>
$("#priorityLevel").kendoValidator({
	 errorTemplate :"...";
 }).data("kendoValidator");
</script>
```
> **注意:**

> - input中必须有**name**字段
> - 前段验证主要为了增强用户体验，告诉用户此字段这样子是否合法，重点校验还是在后端验证
> - errorTemplate错误提示模板，可自定义

2.后端验证

```java
//控制层（以代码维护为例）
@RequestMapping(value = "/sys/code/submit", method = RequestMethod.POST)
    public ResponseData submitCode(@RequestBody List<Code> codes, BindingResult result, HttpServletRequest request) {
         validator.validate(codes, result);//验证
        if (result.hasErrors()) {
            ResponseData rd = new ResponseData(false);
            rd.setMessage(getErrorMessage(result, request));
            return rd;
        }
        IRequest requestContext = createRequestContext(request);
        return new ResponseData(codeService.batchUpdate(requestContext, codes));
    }

//如果验证失败 就会返回失败信息
```
> **注意:**

> - 后端验证验证的是数据处理时的合法性，最终处理数据的是后端，如果数据不合法，则会返回数据错误信息，默认情况是根据dto层对字段所做的限制来进行验证


#### <i class="icon-file"></i> MVVM
viewModel用法
Kendo MVVM是一种MVVM的实现，当然可以跟Kendo 组件(widgets)和数据源(datasource)进行无缝结合

```javascript
var viewModel = kendo.observable({
        model: {
        //model 中可设置绑定初值
            id:5
        },
        //可以自定义函数方法 :如saveFunction方法
        saveFunction: function () {
            $('#grid').data('kendoGrid').saveChanges();
        },
        queryResource: function (e) {
            $('#grid').data('kendoGrid').dataSource.page(1);
        }
    });
```

使用:（**注意data-bind**
）
```html
<div id="view">
    <input data-bind="value: model.id" />
    <button data-bind="click: saveFunction">Display</button>
</div>
```
关于绑定

```html
<div id="view" data-bind="value: model.name"></div>
<script>
    var viewModel = kendo.observable({
        model: {
            name: "John Doe"
        }
    });
    kendo.bind($("#view"), viewModel);
</script>
```
> **注意:**

> - viewModel中绑定是实时的，即viewModel中的name字段发生变化，则input中的value会一起变化，反之，当input中的value值变化，则viewModel中的name字段也会一同发生变化
> - viewModel中的所有字段属性都是可自定义的
> - 绑定需要input中data-bind 和kendo-bind两次绑定 第一是将数值绑定到model上，第二次是将此div绑定到viewModel上，这样才能访问到viewModel的model属性，也就是第一次绑定才有效

重置按钮

```javascript
resetForm : function(e) {
   	 var formData = viewModel.model.toJSON();
   	 for ( var k in formData) {
   		 viewModel.model.set(k, null);
   	 }
}
```

```html
<span class="btn btn-default" data-bind="click:resetForm" type="button"><@spring.message "hap.reset"/></span>

```
> **注意:**

> - viewModel与表单绑定后，model里面的字段变化，input框里的value里一起变化，所以在清空viewModel的同时，清空了表单里的数据

