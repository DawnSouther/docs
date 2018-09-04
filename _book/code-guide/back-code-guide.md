
# 后端编码规范
## 1.格式与命名规范
### 1.1	缩进
考虑到代码在所有设备和终端保持同样的排版，代码中不允许使用 Tab，统一采用4个空格作为缩进单位。
> IDEA 设置 tab 为 4 个空格时， 请勿勾选 `Use tab character`,而在eclipse中，必须勾选`insert spaces for tabs`

### 1.2	换行
每行限制最长120字符，超过120列的行，应按照规范换行。
考虑到如今电脑宽屏为主，120列可以充分利用宽屏空间，也可以避免过多换行。
换行时遵循如下原则:
  * 第二行相对第一行缩进 4 个空格，从第三行开始，不再继续缩进
  * 运算符与下文一起换行
  * 方法调用的点符号与下文一起换行
  * 在多个参数超长，在逗号后换行
  * 在括号前不要换行。
示例：

```java

StringBuffer sb = new StringBuffer();
//超过 120 个字符的情况下，换行缩进 4 个空格，并且方法前的点符号一起换行 sb.append("zi").append("xin")...
          .append("huang")...
          .append("huang")...
          .append("huang");

```

### 1.3	命名规则
* 代码中的命名均不能以`下划线或美元符号`开始，也不能以`下划线或美元符号`结束
*	代码中的命名严禁使用`拼音与英文混合`的方式，更不允许直接使用`中文`的方式,`纯拼音命名`也要避免采用
* 类名使用 `UpperCamelCase` 风格，必须遵从驼峰形式
* 方法名、参数名、成员变量、局部变量都统一使用 `lowerCamelCase` 风格，必须遵从驼峰形式
*	遇到缩写如XML时，仅首字母大写，即loadXmlDocument()而不是loadXMLDocument()
* 常量命名全部大写，单词间用下划线隔开，力求语义表达完整清楚
* 抽象类命名使用 Abstract 或 Base 开头;异常类命名使用 Exception 结尾;测试类 命名以它要测试的类的名称开始，以 Test 结尾
*	局部变量及输入参数不要与类成员变量同名(get/set方法与构造函数除外)
* 中括号是数组类型的一部分，数组定义如下:String[] args
* 接口类中的方法和属性不要加任何修饰符号(public 也不要加)，保持代码的简洁性，并加上有效的 Javadoc 注释。尽量不要在接口里定义变量，如果一定要定义变量，肯定是与接口方法相关，并且是整个应用的基础常量
* Package名必须全部小写，尽量使用单个单词,包名统一使用 单数形式，但是类名如果有复数含义，类名可以使用复数形式。
> 应用工具类包名为com.hand.hap.util、类名为MessageUtils(此规则参考 spring 的框架结构)

* 杜绝完全不规范的缩写，避免望文不知义
> 反例: AbstractClass“缩写”命名成 AbsClass;condition“缩写”命名成 condi，此类 随意缩写严重降低了代码的可阅读性。

### 1.4 代码格式

* 大括号的使用约定。如果是大括号内为空，则简洁地写成{}即可，不需要换行;如果是非空代码块则:
  * 左大括号前不换行
  * 左大括号后换行
  * 右大括号前换行
  * 右大括号后还有else等代码则不换行;表示终止的右大括号后必须换行
* 左小括号和字符之间不出现空格;同样，右小括号和字符之间也不出现空格，如 if (a == b)
* if/for/while/switch/do 等保留字与括号之间都必须加空格
* 任何二目、三目运算符的左右两边都需要加一个空格。包括赋值运算符=、逻辑运算符&&、加减乘除符号等
* 方法参数在定义和传入时，多个参数逗号后边必须加空格。
* IDE的text file encoding设置为UTF-8; IDE中文件的换行符使用Unix格式， 不要使用 windows 格式。

示例：
```java
public static void main(String[] args) {
    // 缩进 4 个空格
    String say = "hello";
    // 运算符的左右必须有一个空格
    int flag = 0;
    // 关键词 if 与括号之间必须有一个空格，括号内的 f 与左括号，0 与右括号不需要空格
    if (flag == 0) {
        System.out.println(say);
    }
    // 左大括号前加空格且不换行;左大括号后换行 
    if (flag == 1) {
        System.out.println("world");
    // 右大括号前换行，右大括号后有 else，不用换行 
    } else {
        System.out.println("ok");
    // 在右大括号后直接结束，则必须换行
    } 
}
```

### 1.5	声明
*	修饰符应该按照如下顺序排列：public, protected, private, abstract, static, final, transient, volatile, synchronized, native, strictfp。
*	类与接口的声明顺序(可用Eclipse的source->sort members功能自动排列):  
  *	静态成员变量 / Static Fields 
  *	静态初始化块 / Static Initializers 
  *	成员变量 / Fields 
  *	初始化块 / Initializers 
  *	构造器 / Constructors 
  *	静态成员方法 / Static Methods 
  *	成员方法 / Methods 
  *	重载自Object的方法如toString(), hashCode() 和main方法
  *	类型(内部类) / Types(Inner Classes) 
  *	同等的类型，按public, protected, private的顺序排列。

## 2	注释规范
### 2.1	注释类型
#### 2.1.1	  Javadoc 注释
Javadoc块的基本格式如下所示：
```java
/**
 * Multiple lines of Javadoc text are written here,
 * wrapped normally…
 */
public int method(String p1){…}
```
或者是以下单行形式：
```java
/** An especially short bit of Javadoc. */
```
基本格式总是OK的。当整个Javadoc块能容纳于一行时(且没有Javadoc标记@XXX)，可以使用单行形式。
所有的抽象方法(包括接口中的方法)都应该使用 Javadoc 注释、除了返回值、参数、异常说明外，还必须指出该方法做什么事情，实现什么功能。

#### 2.1.2	失效代码注释
由`/*…*/`界定，标准的C-Style注释，专用于注释已失效的代码。
```java
/*
 *Comment out the code
 * …
 */
 ```
#### 2.1.3	 代码细节注释
由`//`界定，专用于注释代码细节，即使有多行注释也仍然使用`//`，以便于`/**/`区分开来。
	除了私有变量，不推荐使用行末注释。
```java
class MyClass {
    private int myField; // An end-line comment.
    public void myMethod {
//a very very long
       //comment.
       if (condition1) {
//condition1 comment
          ...
        } else {
//elses condition comment
          ...
        }
    }
}
```



### 2.2	注释的格式
*	注释中的第一个句子要以（英文）句号、问号或者感叹号结束。Javadoc生成工具会将注释中的第一个句子放在方法汇总表和索引中。
*	为了在JavaDoc和IDE中能快速链接跳转到相关联的类与方法，尽量多的使用`@see XXx.MyClass，@see XX.MyClass#find(String)`。
*	Class必须以`@author` 作者名声明作者，不需要声明`@version`与`@date`，由版本管理系统保留此信息。
*	如果注释中有超过一个段落，用`<p>`分隔。
*	示例代码以`<pre></pre>`包裹。
*	标识(java keyword, class/method/field/argument名，Constants) 以`<code></code>`包裹。
*	标识在第一次出现时以`{@linkXXx.Myclass}`注解以便JavaDoc与IDE中可以链接。

### 2.3	注释的内容

#### 2.3.1	可精简的注释内容
注释中的每一个单词都要有其不可缺少的意义，注释里不写"@param name -名字"这样的废话。
	如果该注释是废话，连同标签删掉它，而不是自动生成一堆空的标签，如空的@param name，空的`@return`。

#### 2.3.2	推荐的注释内容
*	对于API函数如果存在契约，必须写明它的前置条件(precondition)，后置条件(postcondition)，及不变式(invariant)。
*	对于调用复杂的API尽量提供代码示例。
*	对于已知的Bug需要声明。
*	在本函数中抛出的unchecked exception尽量用@throws说明。

#### 2.3.3	Null规约
如果方法允许null作为参数，或者允许返回值为null，必须在JavaDoc中说明。
	如果没有说明，方法的调用者不允许使用null作为参数，并认为返回值是nullSafe的。
```java    
/**
 * 获取对象.
 *
 * @ return the object to found or null if not found.
 */
Object get(Integer id){
    ...
}
```

#### 2.3.4	特殊代码注释
*	代码质量不好但能正常运行，或者还没有实现的代码用`//TODO`: 或 `//XXX`:声明
*	存在错误隐患的代码用`//FIXME`:声明

## 3	编程规范
### 3.1	基本规范
 1.	当面对不可知的调用者时，方法需要对输入参数进行校验，如不符合抛出IllegalArgumentException，建议使用Spring的Assert系列函数。
 2.	隐藏工具类的构造器，确保只有static方法和变量的类不能被构造实例。
 3.	变量，参数和返回值定义尽量基于接口而不是具体实现类，如`Map map = new HashMap();`
 4.	代码中不能使用`System.out.println()`，`e.printStackTrace()`，必须使用logger打印信息。
 5. 关于基本数据类型与包装数据类型的使用标准如下:
    * 所有的POJO类属性必须使用包装数据类型。
    * RPC方法的返回值和参数必须使用包装数据类型。
    * 所有的局部变量使用基本数据类型。
 6. 循环体内，字符串的连接方式，使用 StringBuilder 的 append 方法进行扩展。
 7. long 或者 Long 初始赋值时，必须使用大写的 L，不能是小写的 l，小写容易跟数字 1 混淆，造成误解。

### 3.2	异常处理
 1.	重新抛出的异常必须保留原来的异常，即`throw new NewException("message", e);` 而不能写成`throw new NewException("message")`。
 2.	在所有异常被捕获且没有重新抛出的地方必须写日志。  
 3.	如果属于正常异常的空异常处理块必须注释说明原因，否则不允许空的catch块。
 4.	框架尽量捕获低级异常，并封装成高级异常重新抛出，隐藏低级异常的细节。

### 3.3	JDK8
HAP开发以及运行环境统一采用 java 1.8.15（或以上）版本。项目开发人员可使用 java8所提供的特性来是代码更简练。如lambda 表达式等，同时，也要充分利用 java 高版本所带来的便利，如 Annotation 等。

不需要关心的warning信息用`@SuppressWarnings("unused")`, `@SuppressWarnings("unchecked")`, `@SuppressWarnings("serial")` 注释。

