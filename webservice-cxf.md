利用 Apache CXF 发布 WebService
---

HAP 2.0 支持发布 WebService .

测试当前是否支持, 直接访问 url: `http://localhost:8080/core/ws`


## 1. 集成方式
> 本小节仅为通知性内容, 不需要实际操作. 有做过配置文件覆盖的项目, 需要留意!

目前是集成了 `Apache CXF 3.1.7`

因此而带来的依赖更新包括:
* validation-api
   - 由 1.0.0.GA 升级到 1.1.0.Final
* hibernate-validation
   - 由 4.0.2.GA 升级到 5.2.4.Final

其他改动:

* web.xml 添加
```xml
    <servlet>
        <servlet-name>CXFServlet</servlet-name>
        <servlet-class>org.apache.cxf.transport.servlet.CXFServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>CXFServlet</servlet-name>
        <url-pattern>/ws/*</url-pattern>
    </servlet-mapping>
```
> 项目上如果有对 web.xml 做过修改, 敬请留意!

* BaseDTO
  - `__tls` 属性 `get__tls()` 方法增加了 @XmlTransient 注解

## 2.开发方式
参考 hap 中 `spring/cxf-beans-demo.xml`

编写 项目上自定义的 cxf-beans-xxx.xml (xxx 自定义, 不要重复即可)


### 3.关于权限问题
> 默认没有任何控制

hap 预留了 `cxfSecurity.xml` 用作 `/ws/**` url 的权限控制

> 如果项目上对 `applicationContext-security.xml` 做过修改, 敬请留意!

项目上可根据需要覆盖`cxfSecurity.xml`, 自定义配置, 实现控制逻辑.
