HAP 流程设计器教程
---
# 1.开始事件和结束事件
---
对应着流程的开始和结束。
> 应该保证每个分支都可以从开始走到结束

## 开始事件
开始事件用来指明流程在哪里开始。开始事件的类型（流程在接收事件时启动， 还是在指定时间启动，等等），定义了流程如何启动。



> 一般使用第一个空的开始事件就可以了,需要手动启动流程

*属性*
![](/assets/act_vacationReq_bg_v.png)

* 初始化器

  当流程启动时，把当前登录的用户保存到哪个变量名中，可以在表达式里直接引用该变量
* 表单属性

  当执行到开始事件时，所有的流程变量都是可用的，但是可能需要一些用于表单展示的自定义变量，此时可以在表单属性中定义,将会保存到流程的`共享变量`区域
  
  ![](/assets/act_vacationReq_form.png)
  如图是`请假流程`的开始事件的表单属性，在这里定义的变量，在`工作流测试页面`会自动渲染成表单
  支持以下的几种表单属性类型：

 * string (org.activiti.engine.impl.form.StringFormType)
 * long (org.activiti.engine.impl.form.LongFormType)
 * enum (org.activiti.engine.impl.form.EnumFormType)
 * date (org.activiti.engine.impl.form.DateFormType)
 * boolean (org.activiti.engine.impl.form.BooleanFormType)

  > 括号里是作为流程变量存储的对应JAVA类
  表达式和变量
  
  如果设置了`变量`，需要通过变量名来引用变量，表达式同理
  > 默认则是把id作为变量名使用
 
  * 必须   如果勾选了，提交表单时没有提供该属性则抛出异常
  * 可读   不勾选则不会进行自动渲染显示，但是还是可以提交
  * 可写   不够勾选，还提交该属性，也会抛出异常
  
  
## 结束事件
  结束事件表示（子）流程（分支）的结束。 结束事件都是触发事件。 这是说当流程达到结束事件，会触发一个结果。 结果的类型是通过事件的内部黑色图标表示的。
  > 一般使用一个空的结束事件就行了，意味着到达事件时不会指定抛出的结果。 这样，引擎会直接结束当前执行的分支，不会做其他事情。
  
### 自动结束流程（HAP 扩展特性）

当一个任务被拒绝时，绝大多数情况下，这个流程应该直接结束。   
但在工作流中，`审批拒绝`和`审批同意` 仅仅是给变量赋的参数值不同而已，如果希望流程结束，应该加一个 `选择网关`，判断审批结果是否为 `拒绝` ，然后引导流程走向`结束事件`。

显然，这很繁琐，尤其是当流程中`人工任务`节点比较多时。   

HAP 做了一个优化：   
> 如果一个`人工任务` 的下一个节点仍然是 `任务（Task 类型）`，那么 HAP 将会自动插入一个`选择网关`和`终止事件` 来自动做结束流程的操作

这个优化在流程的图上是看不出来的，用户完全无感知。


  
# 2.任务
---

常用的是人工任务，和服务任务

## 人工任务(UserTask)
![](/assets/act_vacationReq_userTask.png)

### 任务派遣
**指定审批人、组**

点击`任务派遣` 弹出对话框

<img width='600' src='/assets/usertask-assignment.png'/>

其中：
* 指派对象    
只能有一个人，可以点击`选择`按钮来从系统中选择员工
* 候选用户   
可以添加多个，每个指定一个`员工工号`
* 候选组   
可以添加多个，每个指定一个`岗位代码`



**动态审批人、组**

与 上面类似，不过动态的审批人和组不是选择的，而是通过表达式计算得来。   

`表达式`有两种情况
   * 引用变量   
   比如`${initiator}`， 其中`initiator` 就是一个变量
   * 调用 service 方法   
   比如`${orgStructure.getDirector(initiator)}`，其中 `orgStructure` 是一个自定的用于工作流的 `bean`，参数则可以使用任意的变量、常量等
   
   > `execution` 是一个固有的、特殊的变量，指代 `org.activiti.engine.delegate.DelegateExecution`，非常有用

关于自定义在工作流中用的 `bean`：   
HAP 提供一个 接口 `com.hand.hap.activiti.custom.IActivitiBean`。   
实现这个接口并被定义注册为 bean，其中的任何 `public` 方法都可以在工作流表达式中直接调用。   
默认 bean 的 名字就是引用名，也可以覆盖接口的默认实现，指定名称。

### 表单的标识Key

   用于在待办明细页面中，动态渲染页面，设置为表单html页面的路径
   
### 表单属性 

   跟开始事件的表单属性一样

**审批动作**
Hap 的工作流在审批的时候默认有两个标准的动作：
* 同意（APPROVED）
* 拒绝（REJECTED）

当审批者点击按钮以后，审批动作 id 会被保存到流程的`共享变量`区域。
key 为 `approveResult`，这个变量可以直接在表达式中使用。

```
${approveResult=='REJECTED'}
```


流程设计时，可以控制这两个按钮显示的文本；也可以控制只显示其中的一个。


允许自定义额外的审批动作，自动动作的 id 同样会保存在 `approveResult` 变量中，通常需要流程显示处理（比如选择网关）。

<img width='920' src='/assets/custom-approve-action.png'/>

### 会签设置
  会签通过设置`多实例`属性来实现，有串行和并行两种模式。
  > 三条竖线表示实例会并行执行。 三条横线表示顺序执行。
  
  <img width='920' src='/assets/activiti_parallel.png'/>

  `集合` 应该返回一个字符串数组，表示用户集合
  > 并行执行 会立刻为所有集合成员创建任务实例
  > 串行执行 会依次创建任务实例，完成一个才会创建下一个
  
  `元素变量` 遍历用户集合的单个对象变量名称
  > 任务派遣 设置${元素变量}即可
  
  `完成条件`  
  > activiti 标准会签,默认有 3 个参数, 可以作为结束条件考量依据.
分别是
 * nrOfInstances(实例个数，根据集集合表达式中获取的对象个数确定)
 * nrOfCompletedInstances(已经完成的实例个数，比如已经同意或者拒绝的)
 * nrOfActiveInstances(还未完成的实例个数)
>
hap 自定义了两个: 
 * nrOfApproved(同意的实例个数)
 * nrOfRejected(拒绝的实例个数)
 
## 服务任务（ServiceTask）
用来调用外部java类

有三种方式

### 类名
在`类名`属性设置类的完整限定名
比如`com.hand.hap.activiti.demo.DemoServiceTask`
直
>  需要实现JavaDelegate接口
   类只会初始化一次， 类似 servlet 的模式。
   
### 代理表达式
执行解析代理对象的表达式，直接通过表达式确定代理对象
比如`$(demoServiceTaskDelegate)`
> 参考com.hand.hap.activiti.demo.components.DemoServiceTaskDelegate 实现JavaDelegate接口和IActivitiBean
注意是用$符号，这是delegateExpression，与普通的表达式有区别

### 表达式
可以调用指定方法
`#{demoServiceTaskDelegate.method1(execution,task2Output)}`

# 3.网关
网关用来控制流程的流向

网关显示成菱形图形，内部有有一个小图标。 图标表示网关的类型

### 单一网关
  内部图标是一个“X”
  当流程执行到这个网关，所有外出顺序流都会被处理一遍。 通过设置`跳转条件`控制流程走向。
> 比如 ${approveResult == 'REJECTED'}，表示拒绝后的流程走向。

  可以设置`默认跳转`，当其他所以顺序流的跳转条件都返回false时触发。
> 注意如果多个顺序流的条件结果为true，单一网关只会选择一个执行。

### 并行网关
  内部是一个“+”图标
  并行网关的功能是基于进入和外出的顺序流的：          

* 分支： 并行后的所有外出顺序流，为每个顺序流都创建一个并发分支。              

* 汇聚： 所有到达并行网关，在此等待的进入分支， 直到所有进入顺序流的分支都到达以后， 流程就会通过汇聚网关。              

  注意，如果同一个并行网关有多个进入和多个外出顺序流，   它就同时具有分支和汇聚功能。   这时，网关会先汇聚所有进入的顺序流，然后再切分成多个并行分支。        

与其他网关的主要区别是，并行网关*不会解析条件*。   即使顺序流中定义了条件，也会被忽略。  

>参考[Activiti5.16中文手册](http://www.mossle.com/docs/activiti/index.html)