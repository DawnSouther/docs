可扩展开发模式
---


## 1.持久层扩展
---
HAP 对 mybatis 的封装还是比较完善的，基本的增删改查，不需要写 SQL，这些归功于 DTO，和它上面的一些注解。

通过 DTO，我们就可以知道表有哪些字段，什么类型，自然增删改查就可以自动完成了。

如果想要给表加一个字段，数据库层面比较好解决，直接加就好了。   
但 DTO 上没有这个字段，就会导致这个字段完全没意义。   

> 怎么样让 DTO 上能增加一个字段呢？写一个子类继承它？似乎没办法融入到原有的逻辑中去。   

HAP 采用的办法是：用`JAVAssist`比较底层的字节码修改，直接去修改这个类。   


### 接口
更具体一点，我们允许用户自定义一个`接口`，接口里面仅允许定义 `getter`，`setter` 方法，并可以在`setter` 上写注解(原先是写在 Field 上)

```java
public interface UserDemoExt {

    @ColumnType(jdbcType = JdbcType.VARCHAR)
    void setUserPhone(String userPhone);

    String getUserPhone();

}
```
请注意一点：
> 如果这个接口`继承`了其他接口，HAP 只会处理这个接口本身定义的方法！   
> 换句话说，你需要保证 DTO 已经具有某个扩展A了，后面加的扩展接口才可以继承A。   

### 配置文件
然后在配置文件中声明   
```xml
<extension-register>
    <extension target="com.hand.hap.testext.dto.UserDemo" extension="com.hand.hap.testext.dto.UserDemoExt">
    </extension>
</extension-register>
```

> 配置文件命名规则：`*.extend.xml`   
> 存放位置：`resources/extension` 子目录任意定义

要对 `com.hand.hap.testext.dto.UserDemo` 进行扩展，   
增加的接口是 `com.hand.hap.testext.dto.UserDemoExt`。   

`extension 节点`可以在一个配置文件中写多个。

关于这个接口的实现，开发者完全不需要关心，`HAP 会自动实现这些方法的功能`。

ONE MORE THING
> DTO 的扩展，仅允许增加新的字段，不允许修改、删除已有的字段！

----
题外话：
> 既然去修改字节码，为何不直接增加字段方法就完了，还要去写一个接口呢？   
> 接口的好处就是，我可以把 dto 强制转换来调用方法。否则只能通过`属性名`去反射操作这个扩展的属性   
> 通过字符串去反射操作，明显不如接口来的方便。   
> 另外接口的方法可以加注解

## 2.Service 层扩展
---
service 层的扩展比较中规中矩，没什么黑科技！

主要是 service 的扩展应用面太窄，很多情况下不是这么简单的，可能需要完全的替换 service 才行。

### 接口
也是有一个 接口 ：`com.hand.hap.extensible.base.IServiceListener<T>`。   
顺便，还加了一个 Adaptor `com.hand.hap.extensible.base.ServiceListenerAdaptor<T>`。   

> 虽然起得名字叫 `Listener` ，但这个模式更像 `Filter` 😂

```java
@Component
@ServiceListener(target = UserDemoServiceImpl.class)
public class UserDemoServiceListener extends ServiceListenerAdaptor<UserDemo> {

    @Override
    public void beforeUpdate(IRequest request, UserDemo record, ServiceListenerChain<UserDemo> chain) {
        System.out.println(getClass().getSimpleName() + "::beforeUpdate");
        System.out.println("extension attribute: userPhone:" + ((UserDemoExt) record).getUserPhone());
        System.out.println("extension attribute: endActiveTime:" + ((UserDemoExt2) record).getEndActiveTime());
        chain.beforeUpdate(request, record);
    }

    @Override
    public void afterUpdate(IRequest request, UserDemo record, ServiceListenerChain<UserDemo> chain) {
        System.out.println(getClass().getSimpleName() + "::afterUpdate");
        chain.afterUpdate(request, record);
    }
}
```

目前就只有 3 类（insert，update，delete） 6个 方法 （分别有 before，after）

在这些方法里面，你可以完成一些简单的逻辑，也可以将参数 dto 替换成其他的可适配的对象。

记得在最后调用 `chain.xxxx(request, record);`   
除非你不想让其他的 Listener 执行了。


### 注解
@ServiceListener
这个注解最主要的目的还是告诉 HAP，要对哪个 Service 进行 扩展。   
请注意，必须具体到`实现类`，不能只写一个抽象类或接口！   

另外还可以粗略的控制顺序
* before   
必须在哪哪些扩展之前执行
* after   
必须在哪些扩展之后执行


## 3.View 扩展

请参考 [页面扩展](viewTag/mergeViewTag.md)


