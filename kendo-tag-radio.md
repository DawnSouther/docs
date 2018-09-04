### radio标签

**单选框**

```xml
 <h:radio bind="value:name" id="radio" layout="vertical">
 <h:items>
 <h:item label="A" value="1"/>
 <h:item label="B" value="2"/>
 <h:item label="C" value="3"/>
 </h:items>
 </h:radio>

```

### **主要属性**

| 属性名 | 类型 | 描述 |
| --- | --- | --- |
| layout | String | 单选框布局，vertical表示竖直排列，hrizental表示水平排列 |
| items | Array | 单选框数据 |
| item | Object | 单选框数据选项，两个属性。label表示标题，value表示值 |
| enable | Boolean | 是否是可点击的 |

### **用法示例**


```xml
 <h:radio id="radio" layout="vertical" enable="false">
 <h:items>
 <h:item label="A" value="1"/>
 <h:item label="B" value="2"/>
 <h:item label="C" value="3"/>
 </h:items>
 </h:radio>
```

