# 标签合并

------
在标签开发模式下，新增加了标签合并功能，可以对已有的标签进行个性化改造。


* **首先创建合并文件**

我们以系统中的demo.view为例进行个性化配置

1.创建demo.conf配置文件
```xml
<?xml version="1.0" encoding="UTF-8"?> 
<patch xmlns:h="http://www.hand-china.com/hap" >

    <!--在节点后插入-->
    <xpath expr="h:grid[@id='grid']/*/h:column[@field='url']" action="after">
        <h:column field="url2" title='<@spring.message "resource.url"/>2' width="200"/>
    </xpath>
    
    <!--在节前后插入-->
    <xpath expr="h:grid[@id='grid']/*/h:column[@field='lov']" action="before">
        <h:column field="url3" title='<@spring.message "resource.url"/>3' width="200"/>
    </xpath>
    
    <!--删除节点-->
    <xpath expr="h:grid[@id='grid']/*/h:column[@field='phone']" action="remove"/>
    
    <!--替换节点-->
    <xpath expr="h:grid[@id='grid']/*/h:column[@field='name']" action="replace">
        <h:column field="name1" title='测试姓名1' width="120" editor="nameEditor"/>
        <h:column field="name2" title='测试姓名2' width="120" editor="nameEditor"/>
    </xpath>
    
    <!--设置节点属性-->
    <xpath expr="h:grid[@id='grid']" action="attribute">
        <attribute name="rownumber" value="false"/>
    </xpath>
</patch>
```
**说明**
> * xpath 节点是每一步个性化动作
* expr 按照XPATH语法查找节点 **注意: 仅实现了按照属性查找和路径通配符**
* action 对当前节点进行个性化
  * after 在当前节点后插入
  * before 在当前节点前插入
  * remove 删除当前节点
  * replace 替换当前节点
  * attribute 设置当前节点属性

2.对需要个性化的页面进行配置

(a) 打开「资源管理」功能，查找 demo.view 资源，点击个性化配置按钮
![](/assets/QQ截图20161230151924.png)

(b) 新增 demo.conf 的配置，并且启用
![](/assets/QQ截图20161230151948.png)

(c) 成功保存后，刷新 demo.view 观察效果

