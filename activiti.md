 HAP 工作流开发指南
---
## UserTask
---
### 指定审批人、组

在流程设计器中，选定`人工任务`节点，右边属性栏，点击`任务派遣` 弹出对话框

<img width='600' src='/assets/usertask-assignment.png'/>

其中：
* 指派对象    
只能有一个人，可以点击`选择`按钮来从系统中选择员工
* 候选用户   
可以添加多个，每个指定一个`员工工号`
* 候选组   
可以添加多个，每个指定一个`岗位代码`



### 动态审批人、组

与 上面类似，不过动态的审批人和组不是选择的，而是通过表达式计算得来。   

表达式有两种情况
   * 引用变量   
   比如`${initiator}`， 其中`initiator` 就是一个变量
   * 调用 service 方法   
   比如`${orgStructure.getDirector(initiator)}`，其中 `orgStructure` 是一个自定的用于工作流的 `bean`，参数则可以使用任意的变量、常量等
   
   > `execution` 是一个固有的、特殊的变量，指代 `org.activiti.engine.delegate.DelegateExecution`，非常有用

关于自定义在工作流中用的 `bean`：   
HAP 提供一个 接口 `com.hand.hap.activiti.custom.IActivitiBean`。   
实现这个接口并被定义注册为 bean，其中的任何 `public` 方法都可以在工作流表达式中直接调用。   
默认 bean 的 名字就是引用名，也可以覆盖接口的默认实现，指定名称。

### 审批动作
Hap 的工作流在审批的时候默认有两个标准的动作：
* 同意（APPROVED）
* 拒绝（REJECTED）

当审批者点击按钮以后，审批动作 id 会被保存到流程的共享变量区域。
key 为 `approveResult`，这个变量可以直接在表达式中使用。

```
${approveResult=='REJECTED'}
```


流程设计时，可以控制这两个按钮显示的文本；也可以控制只显示其中的一个。


允许自定义额外的审批动作，自动动作的 id 同样会保存在 `approveResult` 变量中，通常需要流程显示处理（比如选择网关）。

<img width='920' src='/assets/custom-approve-action.png'/>



### 自动结束流程（HAP 扩展特性）

当一个任务被拒绝时，绝大多数情况下，这个流程应该直接结束。   
但在工作流中，`审批拒绝`和`审批同意` 仅仅是给变量赋的参数值不同而已，如果希望流程结束，应该加一个 `选择网关`，判断审批结果是否为 `拒绝` ，然后引导流程走向`结束事件`。

显然，这很繁琐，尤其是当流程中`人工任务`节点比较多时。   

HAP 做了一个优化：   
> 如果一个`人工任务` 的下一个节点仍然是 `任务（Task 类型）`，那么 HAP 将会自动插入一个`选择网关`和`终止事件` 来自动做结束流程的操作

这个优化在流程的图上是看不出来的，用户完全无感知。

### 会签设置

自定义属性：`nrOfApproved`，`nrOfRejected`

集合

变量

任务派遣

完成条件

## 网关
---

选择网关

> 选择网关`必须` 指定一个默认的连线，否则会出现不固定的行为。

并行网关

> 并行网关会在`所有连入`的连线都激活以后才会开始执行

## ServiceTask
---

## 待办通知
---

## 审批链
---
审批链功能不是 Activiti 的标准功能，属于 HAP 开发的外挂性质的扩展。

功能入口是在 `流程设计` 界面。

<img width='460' src='/assets/approve_chain_entry.png'/>

点击 编辑图标 进入该流程的审批链配置界面

<img width='900' src='/assets/approve_chain@2x.png'/>


在这个编辑页面中，所有的 `UserTask` 节点会以 标签页的形式自动列出来。

切换标签页，下方表格中的数据会自动刷新为对应节点的数据。

上方的 操作按钮 是针对每个 标签页 操作的，并非针对整个页面。

主要属性说明：

* 名称   
自定义，任意。`不允许重复`。    
节点原始名称 和 审批链的名称 拼接起来 作为运行时的名称。
* 审批人   
内置 3 种 
   * 申请人（自己）
   * 上级（申请人直接领导）
   * 申请人部门领导   
   
   其他情况统统归为 `自定义表达式`
   
 > 注意，这个功能要求流程的初始化器（Initiator）必须指定为 `initiator`

* 审批岗位   
支持选择系统中的`岗位`，也支持自定义的`表达式`

> 目前在指定了审批人的情况下，再指定审批岗位是无效果的

* 表单   
允许该轮次的审批显示指定的单据明细页面。   
如果没有指定，则显示该节点原始设置的值。
* 顺序号   
用来控制审批的先后 （目前尚不支持多人同时在同一轮次审批）
* 跳过条件   
表达式，有值，且值为 true 时，当前轮次跳过。   
默认留空，表示不跳过。
* 当前轮次因为 `跳过条件`被跳过时，是否直接结束审批链
* 启用   
高优先级过滤条件（不启用肯定就没有作用）
* 描述   
无明确用途

---
在设计器中设计审批节点时，由审批链控制的节点，不需要在去指定 `任务派遣`，可以完全在审批链中定义。即使指定了，也会被审批链中的值覆盖！

除了与审批人、岗位有关的属性以外，其他属性的含义、作用保持不变。

`名称`稍微有变化：会和审批链的名称拼接在一起


> 请注意：目前审批链还不支持多例（会签）
