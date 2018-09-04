# Button标签

此按钮标签为KendoUI原生按钮


```xml
<h:button id="button" click="clickFunction"/>
```

### **主要属性**

| 属性名 | 类型 | 描述
| --- | --- | --- |
| enable | Boolean | 是否启用 |
| icon | String | 按钮图标iconFont|
| imageUrl | String | 按钮图片 |
| spriteCssClass | String | 样式 |
| click | Function | 点击函数 |
| text | String | 按钮文本 |
> 属性用法请参考KendoUI文档 [http://docs.telerik.com/kendo-ui/api/javascript/ui/button](http://docs.telerik.com/kendo-ui/api/javascript/ui/button) 


### **用法示例**

```javascript
//按钮点击事件响应函数
function onButtonClick(){
    //do something
}
```

```xml
<h:button id="button" click="onButtonClick" enable="true" imageUrl="/images/edit-icon.gif"  spriteCssClass="myEditIcon" text="按钮"/>
```


