
# LigerUI开发实践
##### njq.niu@hand-chian.com
---------------------------------------


## LigerUI Grid

### 增加canSelect可选择函数
grid中每一行的复选框可以动态通过canSelect函数进行

例如：ID大于8的不能选中
```javascript
canSelect:function(item){
   return item.ID > 8;  
},
```

### 如何动态控制一列中数据是否可以编辑
往往有这样的需求，某一行的数据是否可以编辑要取决于其他字段的值。
grid可以通过onBeforeEdit事件函数来实现
```javascript
onBeforeEdit:function(item){
    //获取编辑的列名name,以及当前行的数据data
    var name = item.column.name,data = item.record;
    //判断编辑的列名是否是"RealName"
     if(name == 'RealName'){
         //ID小于10的返回false表明不能编辑
         return !(data.ID<10);
     }
     return true;
 }
```


### form上实现lov
form上lov的实现代码
```javascript
{
    display : "用户 2",
    name : "userId",//最终选中的值字段名
    type : "popup",
    newline : false,
    //如果不需要把lov选中的textField值放进data中那么这个属性可以不设置，如果需要放在data中就需要设置
    textField:'userName',
    options:{
        readonly:false,//是否可编辑
        valueField : 'userId',//选中的valueField
        textField : 'loginName',//选中的textField
        grid : {
            columns : [ {
                display : 'loginName',
                name : "loginName",
                width : 150,
                type : "int",
                align : "left"
            }, {
                display : 'name',
                name : "name",
                width : 150,
                type : "text",
                align : "left"
            }, {
                display : "email",
                name : "email",
                width : 150,
                type : "text",
                align : "left"
            }, {
                display : "phone",
                name : "phone",
                width : 150,
                type : "text",
                align : "left"
            } ],
            //数据查询的url
            url : '${base.contextPath}/sys/user/select' 
        },
        //lov上的查询条件
        condition : {
            inputWidth : 150,
            labelWidth : 70,
            fields : [ {
                display : 'loginName',
                name : "loginName",
                newline : true,
                type : "text"
            }, {
                display : 'name',
                name : "name",
                newline : false,
                type : "text"
            } ]
        }
    }
    
}
```

### grid上实现lov
```javascript
{
    display : 'lov',
    name : 'userId',
    width : 220,
    //很重要，否则无法显示选中的描述
    textField:'loginName',
    editor : {
        type : 'popup',
        valueField : 'userId',//lov的valueField
        textField : 'loginName',//lov的textField
        grid : {
            columns : [ {
                display : '<@spring.message "type.com.hand.hap.dto.sys.user.loginname"/>',
                name : "loginName",
                width : 150,
                type : "int"
            }, {
                display : '<@spring.message "type.com.hand.hap.dto.sys.user.name"/>',
                name : "name",
                width : 150,
                type : "text"
            }, {
                display : "email",
                name : "email",
                width : 150,
                type : "text"
            }, {
                display : "phone",
                name : "phone",
                width : 150,
                type : "text"
            } ],
            url : '${base.contextPath}/sys/user/select'
        },
        //lov上的查询条件
        condition : {
            inputWidth : 150,
            labelWidth : 70,
            fields : [ {
                display : '<@spring.message "type.com.hand.hap.dto.sys.user.loginname"/>',
                name : "loginName",
                newline : true,
                type : "text"
            }, {
                display : '<@spring.message "type.com.hand.hap.dto.sys.user.name"/>',
                name : "name",
                newline : false,
                type : "text"
            } ]
        }
    }
}
```

### form上的select联动
form上select的联动是通过onBeforeOpen事件函数来实现，在打开select的时候触发设置查询参数
```javascript
{
    display: "Employee",
    name: "employeeId",
    type: "select",
    textField:"employeeName",
    options: {
        readonly: false,
        valueField: 'employeeId',
        textField: 'employeeName',
        url: 'host/employee/query',
        triggerToLoad: true,//注意
        onBeforeOpen: function() {
            //这里设置查询参数
            this.setParm('deptId', liger.get('deptId').getValue());
            this.triggerLoaded = false;//注意
        }
    }
}
```

### form上的lov联动
form上lov的动态参数设置通过onLoadData事件函数实现
```javascript
{
    display: "角色",
    name: "roleId",
    type: "popup",
    textField: 'roleName',
    options:{
         // 其他popup配置
        onLoadData: function() {
             //设置参数
            this.setParm('deptId', $.ligerui.get('deptId').getValue())
        }
    })
}
```
对于通用lov，可以通过extend方式扩展onLoadData事件
```javascript
options: $.extend($ {lovService.getLov(base.contextPath, base.locale, "lov_role")},{
    onLoadData: function() {
        this.setParm('deptId', $.ligerui.get('deptId').getValue())
    }
})
```

### grid上的select联动
```javascript
display: 'Emplooyee',
width: 150,
name: 'employeeId',
type: 'text',
textField: 'employeeName',
editor: {
    type: 'select',
    textField: 'name',
    valueField: 'id',
    url: '${base.contextPath}/demo/user/query',
    //设置参数
    staticOptions: function(e) {
        e.column.editor.parms = {
            name: e.record.name,
            age: 11
        }
    }
```

### grid上lov联动
```javascript
{
    display : '通用lov3',  
	name : 'roleId',
	width : 150,
	textField:'roleName',
	align: 'left',
	editor: $.extend(${lovService.getLov(base.contextPath,base.locale, "lov_role")},{
	    staticOptions : function(e){
	        //设置参数
		    e.column.editor.parms = {
			    deptId : e.record.deptId
		    }
		}
	})
}
```
