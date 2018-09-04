# checkbox 标签

checkbox框

```xml
<h:checkbox id="testCheckbox"/>

```

### **主要属性**

| 属性名 | 类型 | 描述 |
| --- | --- | --- |
| checkedValue | String | 选中时input框的值 |
| uncheckedValue | String | 未选中时input框的值 |
| readonly | Boolean | 是否是可点击的 |


### **用法示例**

```xml
<h:checkbox id="testCheckbox"
bind="value:model.checked" readonly="false" checkedValue="Y" uncheckedValue="N"/>

```

