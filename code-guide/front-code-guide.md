# 前端开发规范

此开发规范主要实现的目标：**代码一致性**和**最佳实践**。通过代码风格的一致性，降低维护代码的成本以及提高多人协作开发的效率。同时遵守最佳实践，不但能确保页面性能得到最佳优化和高效的代码，而且降低bug发生的概率。

此开发规范是通过长期的开发中过程中积累下来的经验和参考其它规范/指南制定的，它只是起指导作用，除个别条目强制之外，大多数为非强制约束，开发者可根据自己的实际情况自行决定是否需要遵守。


# <span id="basic">基本原则</span>
-------------------

#### 文件命名
* 文件名用英文单词，多个单词用驼峰命名法；
* 避免特殊含义的字符出现，防止个别浏览器当做广告拦截。
>例如：`ad`、`ads`、`adv`、`banner`、`sponsor`、`gg`、`guangg`、`guanggao`等

#### 文件编码
* **使用不带 `BOM` 的 UTF-8 编码。**
* 在 HTML中指定编码 `<meta charset="utf-8">` ；
* 无需使用 `@charset` 指定样式表的编码，它默认为 `UTF-8` ；
```
# 设定字符编码是通过 <meta> 标签进行
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
# HTML5，我们只需要这样写
<meta charset="utf-8">
```

#### 结构、样式、行为分离
* 尽量确保文档和模板只包含 `HTML` 结构，样式都放到样式表里，行为都放到脚本里。

#### 缩进
* 统一**四个空格**缩进（可将编辑器 tab 键为空格代替），不要使用 `Tab` 或者 `Tab`、空格混搭，以避免不同编辑器下代码格式错乱。

#### 尾部空格
* 避免一行代码尾部出现空格，防止**`diff`**

#### 一律使用小写字母
```html
<!-- Recommended -->
<img src="google.png" alt="Google">

<!-- Not recommended -->
<A HREF="/">Home</A>
```

```css
/* Recommended */
color: #e5e5e5;

/* Not recommended */
color: #E5E5E5;
```

#### 省略外链资源 URL 协议部分
省略外链资源（图片及其它媒体资源）URL 中的 `http` / `https` 协议，使 URL 成为相对地址，避免 [Mixed Content](https://developer.mozilla.org/en-US/docs/Security/MixedContent) 问题，减小文件字节数。

**其它协议（`ftp` 等）的 URL 不省略。**
```html
<!-- Recommended -->
<script src="//www.google.com/js/gweb/analytics/autotrack.js"></script>

<!-- Not recommended -->
<script src="http://www.google.com/js/gweb/analytics/autotrack.js"></script>
```

```css
/* Recommended */
.example {
  background: url(//www.google.com/images/example);
}

/* Not recommended */
.example {
  background: url(http://www.google.com/images/example);
}
```

#### 统一注释
通过配置编辑器，可以提供快捷键来输出一致认可的注释模式。
#####HTML 注释
- 模块注释
```html
<!-- 文章列表列表模块 -->
<div class="article-list">
...
</div>
```

- 区块注释
```html
<!--
@name: Drop Down Menu
@description: Style of top bar drop down menu.
@author: Ashu(Aaaaaashu@gmail.com)
-->
```

#####CSS 注释
组件块和子组件块以及声明块之间使用一空行分隔，子组件块之间三空行分隔；
```css
/*!
 * Bootstrap v3.3.5 (http://getbootstrap.com)
 * Copyright 2011-2015 Twitter, Inc.
 * Licensed under MIT (https://github.com/twbs/bootstrap/blob/master/LICENSE)
 */
.selector {
  padding: 15px;
  margin-bottom: 15px;
}

/* 注释块 */
.selector-secondary {
  display: block; /* 注释*/
}

.selector-three {
  display: span;
}
```

#####JavaScript 注释
- 单行注释

必须独占一行。`//` 后跟一个空格，缩进与下一行被注释说明的代码一致。

- 多行注释

避免使用 `/*...*/` 这样的多行注释。有多行注释内容时，使用多个单行注释。

- 函数/方法注释
1. 函数/方法注释必须包含函数说明，有参数和返回值时必须使用注释标识。；
2. 参数和返回值注释必须包含类型信息和说明；
3. 当函数是内部函数，外部不可访问时，可以使用 @inner 标识；

```javascript
/**
 * 函数描述
 *
 * @param {string} p1 参数1的说明
 * @param {string} p2 参数2的说明，比较长
 *     那就换行了.
 * @param {number=} p3 参数3的说明（可选）
 * @return {Object} 返回值描述
 */
function foo(p1, p2, p3) {
    var p3 = p3 || 10;
    return {
        p1: p1,
        p2: p2,
        p3: p3
    };
}
```

- 文件注释

文件注释用于告诉不熟悉这段代码的读者这个文件中包含哪些东西。 应该提供文件的大体内容, 它的作者, 依赖关系和兼容性信息。如下:

```javascript
/**
 * @fileoverview Description of file, its uses and information
 * about its dependencies.
 * @author xxx@xxxx.com
 * Copyright 2015 XXX Inc. All Rights Reserved.
 */
```

#### 代码验证
* 使用 [W3C HTML Validator](http://validator.w3.org/) 来验证你的HTML代码有效性；
* 使用 [W3C CSS Validator](http://jigsaw.w3.org/css-validator/validator.html.zh-cn) 来验证你的CSS代码有效性；


# <span id="html">HTML </span>
-------------------
尽量遵循 HTML 标准和语义，使用最少的标签并保持最小的复杂度。

## <span id="html-general">通用约定</span>

#### 标签
* 自闭合（self-closing）标签，无需闭合 ( 例如： `img` `input` `br` `hr` 等 )；
* 可选的闭合标签（closing tag），需闭合 ( 例如：`</li>` 或 `</body>` )；
* 尽量减少标签数量；

```html
<img src="images/google.png" alt="Google">
<input type="text" name="title">

<ul>
  <li>Style</li>
  <li>Guide</li>
</ul>

<!-- Not recommended -->
<span class="avatar">
  <img src="...">
</span>

<!-- Recommended -->
<img class="avatar" src="...">
```

#### Class 与 ID
* class 应以功能或内容命名，不以表现形式命名；
* class 与 id 单词字母小写，多个单词组成时，采用中划线`-`分隔；
* 使用唯一的 id 作为 Javascript hook, 同时避免创建无样式信息的 class；

```html
<!-- Not recommended -->
<div class="j-hook left contentWrapper"></div>

<!-- Recommended -->
<div id="j-hook" class="sidebar content-wrapper"></div>
```

#### 属性顺序
HTML 属性应该按照特定的顺序出现以保证易读性。
* id
* class
* name
* data-xxx
* src, for, type, href
* title, alt
* aria-xxx, role

```html
<a id="..." class="..." data-modal="toggle" href="###"></a>

<input class="form-control" type="text">

<img src="..." alt="...">
```

#### 引号
属性的定义，统一使用双引号。

```html
<!-- Not recommended -->
<span id='j-hook' class=text>Google</span>

<!-- Recommended -->
<span id="j-hook" class="text">Google</span>
```

#### 嵌套
`a 不允许嵌套 div`这种约束属于语义嵌套约束，与之区别的约束还有严格嵌套约束，比如`a 不允许嵌套 a`。

严格嵌套约束在所有的浏览器下都不被允许；而语义嵌套约束，浏览器大多会容错处理，生成的文档树可能相互不太一样。

**语义嵌套约束**
* `<li>` 用于 `<ul>` 或 `<ol>` 下；
* `<dd>`, `<dt>` 用于 `<dl>` 下；
* `<thead>`, `<tbody>`, `<tfoot>`, `<tr>`, `<td>` 用于 `<table>` 下；

**严格嵌套约束**
* inline-Level 元素，仅可以包含文本或其它 inline-Level 元素;
* `<a>`里不可以嵌套交互式元素`<a>`、`<button>`、`<select>`等;
* `<p>`里不可以嵌套块级元素`<div>`、`<h1>~<h6>`、`<p>`、`<ul>/<ol>/<li>`、`<dl>/<dt>/<dd>`、`<form>`等。

更多详情，参考[WEB标准系列-HTML元素嵌套](http://www.smallni.com/element-nesting/)

#### 布尔值属性
HTML5 规范中 `disabled`、`checked`、`selected` 等属性不用设置值。
```html
<input type="text" disabled>

<input type="checkbox" value="1" checked>

<select>
  <option value="1" selected>1</option>
</select>
```

## <span id="html-semantic">语义化</span>
没有 `CSS` 的 `HTML` 是一个语义系统而不是 UI 系统。

通常情况下，每个标签都是有语义的，所谓语义就是你的衣服分为外套， 裤子，裙子，内裤等，各自有对应的功能和含义。所以你总不能把内裤套在脖子上吧。-- 一丝

此外语义化的 `HTML` 结构，有助于机器（搜索引擎）理解，另一方面多人协作时，能迅速了解开发者意图。

#### 常见标签语义

| 标签 | 语义 |
| -- | -- |
| `<p>` | 段落 |
| `<h1> <h2> <h3> ...` | 标题 |
| `<ul>` | 无序列表 |
| `<ol>` | 有序列表 |
| `<blockquote>` | 大段引用 |
| `<cite>` | 一般引用 |
| `<b>` | 为样式加粗而加粗 |
| `<strong>` | 为强调内容而加粗 |
| `<i>` | 为样式倾斜而倾斜 |
| `<em>` | 为强调内容而倾斜 |
| `code` | 代码标识 |
| `abbr` | 缩写 |

### 示例
将你构建的页面当作一本书，将标签的语义对应的其功能和含义；
* 书的名称：`<h1>`
* 书的每个章节标题: `<h2>`
* 章节内的文章标题: `<h3>`
* 小标题/副标题: `<h4> <h5> <h6>`
* 章节的段落: `<p>`


## <span id="html-head">HEAD</span>

#### 文档类型
为每个 HTML 页面的第一行添加标准模式（standard mode）的声明， 这样能够确保在每个浏览器中拥有一致的表现。

```html
<!DOCTYPE html>
```

#### 语言属性

```html
<!-- 中文 -->
<html lang="zh-Hans">

<!-- 简体中文 -->
<html lang="zh-cmn-Hans">

<!-- 繁体中文 -->
<html lang="zh-cmn-Hant">

<!-- English -->
<html lang="en">
```

#### 字符编码
* 以无 BOM 的 utf-8 编码作为文件格式;
* 指定字符编码的 meta 必须是 head 的第一个直接子元素；

```html
<html>
  <head>
    <meta charset="utf-8">
    ......
  </head>
  <body>
    ......
  </body>
</html>
```

#### IE 兼容模式
优先使用最新版本的IE 和 Chrome 内核

```html
<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
```

#### SEO 优化
```html
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
    <!-- SEO -->
    <title>Style Guide</title>
    <meta name="keywords" content="your keywords">
    <meta name="description" content="your description">
    <meta name="author" content="author,email address">
</head>
```

#### viewport
* `viewport`: 一般指的是浏览器窗口内容区的大小，不包含工具条、选项卡等内容；
* `width`: 浏览器宽度，输出设备中的页面可见区域宽度；
* `device-width`: 设备分辨率宽度，输出设备的屏幕可见宽度；
* `initial-scale`: 初始缩放比例；
* `maximum-scale`: 最大缩放比例；

为移动端设备优化，设置可见区域的宽度和初始缩放比例。
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

#### favicon
在未指定 favicon 时，大多数浏览器会请求 Web Server 根目录下的 favicon.ico 。为了保证 favicon 可访问，避免404，必须遵循以下两种方法之一：
* 在 Web Server 根目录放置 favicon.ico 文件；
* 使用 link 指定 favicon；

```html
<link rel="shortcut icon" href="path/to/favicon.ico">
```

#### HEAD 模板
```html
<!DOCTYPE html>
<html lang="zh-cmn-Hans">
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
    <title>Style Guide</title>
    <meta name="description" content="不超过150个字符">
    <meta name="keywords" content="">
    <meta name="author" content="name, email@gmail.com">

    <!-- 为移动设备添加 viewport -->
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="shortcut icon" href="path/to/favicon.ico">
</head>
```

# <span id="css">CSS规范</span>
-------------------
## <span id="css-general">通用约定</span>
#### 代码组织
- 以组件为单位组织代码段；
- 制定一致的注释规范；
- `组件块和子组件块`以及`声明块`之间使用**一空行**分隔，`子组件块`之间**三空行**分隔；
- 如果使用了多个 CSS 文件，将其按照组件而非页面的形式分拆，因为页面会被重组，而组件只会被移动；

良好的注释是非常重要的。请留出时间来描述组件（component）的工作方式、局限性和构建它们的方法。不要让你的团队其它成员 来猜测一段不通用或不明显的代码的目的。

提示：通过配置编辑器，可以提供快捷键来输出一致认可的注释模式。

```css
/*!
 * Bootstrap v3.3.5 (http://getbootstrap.com)
 * Copyright 2011-2015 Twitter, Inc.
 * Licensed under MIT (https://github.com/twbs/bootstrap/blob/master/LICENSE)
 */
.selector {
  padding: 15px;
  margin-bottom: 15px;
}

.selector-secondary {
  display: block; /* 注释*/
}

.selector-three {
  display: span;
}
```

#### Class 和 ID
- 使用语义化、通用的命名方式；
- 使用连字符 - 作为 ID、Class 名称界定符，不要驼峰命名法和下划线；
- 避免选择器嵌套层级过多，尽量少于 3 级；
- 避免选择器和 Class、ID 叠加使用；

出于[性能考量](http://www.stevesouders.com/blog/2009/06/18/simplifying-css-selectors/)，在没有必要的情况下避免元素选择器叠加 Class、ID  使用。

元素选择器和 ID、Class 混合使用也违反关注分离原则。如果HTML标签修改了，就要再去修改 CSS 代码，不利于后期维护。
```css
/* Not recommended */
.red {}
.box_green {}
.page .header .login #username input {}
ul#example {}

/* Recommended */
#nav {}
.box-video {}
#username input {}
#example {}
```


#### CSS 样式中尽可能压缩样式

```css
/* Not recommended */
padding-bottom: 2em;
padding-left: 1em;
padding-right: 1em;
padding-top: 0;
border-top-style: none;
line-height: 1.6;
font-family: palatino, georgia, serif;
font-size: 100%;

/* Recommended */
padding: 0 1em 2em;
border-top: 0;
font: 100%/1.6 palatino, georgia, serif;
```
#### 0 的时候去除单位

```css
margin: 0;
padding: 0;
```

#### 声明块格式
- 选择器分组时，保持独立的选择器占用一行；
- 声明块的左括号 `{` 前添加一个空格；
- 声明块的右括号 `}` 应单独成行；
- 声明语句中的 `:` 后应添加一个空格；
- 声明语句应以分号 `;` 结尾；
- 一般以逗号分隔的属性值，每个逗号后应添加一个空格；
- `rgb()`、`rgba()`、`hsl()`、`hsla()` 或 `rect()`  括号内的值，逗号分隔，但逗号后不添加一个空格；
- 对于属性值或颜色参数，省略小于 1 的小数前面的 0 （例如，`.5` 代替 `0.5`；`-.5px` 代替 `-0.5px`）；
- 十六进制值应该全部小写和尽量简写，例如，`#fff` 代替 `#ffffff`；
- 避免为 0 值指定单位，例如，用 `margin: 0;` 代替 `margin: 0px;`；

```css
/*  Not recommended  */
.selector, .selector-secondary, .selector[type=text] {
  padding:15px;
  margin:0px 0px 15px;
  background-color:rgba(0, 0, 0, 0.5);
  box-shadow:0px 1px 2px #CCC,inset 0 1px 0 #FFFFFF
}

/* Recommended */
.selector,
.selector-secondary,
.selector[type="text"] {
  padding: 15px;
  margin-bottom: 15px;
  background-color: rgba(0,0,0,.5);
  box-shadow: 0 1px 2px #ccc, inset 0 1px 0 #fff;
}
```

#### 声明顺序
相关属性应为一组，推荐的样式编写顺序
1. Positioning
2. Box model
3. Typographic
4. Visual

由于定位（positioning）可以从正常的文档流中移除元素，并且还能覆盖盒模型（box model）相关的样式，因此排在首位。盒模型决定了组件的尺寸和位置，因此排在第二位。

其他属性只是影响组件的内部（inside）或者是不影响前两组属性，因此排在后面。

```css
.declaration-order {
  /* Positioning */
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  z-index: 100;

  /* Box model */
  display: block;
  box-sizing: border-box;
  width: 100px;
  height: 100px;
  padding: 10px;
  border: 1px solid #e5e5e5;
  border-radius: 3px;
  margin: 10px;
  float: right;
  overflow: hidden;

  /* Typographic */
  font: normal 13px "Helvetica Neue", sans-serif;
  line-height: 1.5;
  text-align: center;

  /* Visual */
  background-color: #f5f5f5;
  color: #fff;
  opacity: .8;

  /* Other */
  cursor: pointer;
}
```

#### 引号使用
`url()` 、属性选择符、属性值使用双引号。
参考 [Is quoting the value of url() really necessary?](http://stackoverflow.com/questions/2168855/is-quoting-the-value-of-url-really-necessary)
```css
/* Not recommended */
@import url(//www.google.com/css/maia.css);

html {
  font-family: 'open sans', arial, sans-serif;
}

/* Recommended */
@import url("//www.google.com/css/maia.css");

html {
  font-family: "open sans", arial, sans-serif;
}

.selector[type="text"] {

}
```

#### 媒体查询（Media query）的位置
将媒体查询放在尽可能相关规则的附近。不要将他们打包放在一个单一样式文件中或者放在文档底部。如果你把他们分开了，将来只会被大家遗忘。

```css
.element { ... }
.element-avatar { ... }
.element-selected { ... }

@media (max-width: 768px) {
  .element { ...}
  .element-avatar { ... }
  .element-selected { ... }
}
```

#### 尽量不要使用 `@import`
与 `<link>` 相比，`@import` 要慢很多，不光增加额外的请求数，还会导致不可预料的问题。

替代办法：
- 使用多个 <link> 元素；
- 通过 Sass 或 Less 类似的 CSS 预处理器将多个 CSS 文件编译为一个文件；
- 其他 CSS 文件合并工具；

#### 链接的样式顺序：
`a:link -> a:visited -> a:hover -> a:active（LoVeHAte）`

# <span id="javascript">JavaScript</span>
-------------------
## <span id="javascript-general">通用约定</span>
#### 注释
**原则**
-  As short as possible（如无必要，勿增注释）：尽量提高代码本身的清晰性、可读性。
-  As long as necessary（如有必要，尽量详尽）：合理的注释、空行排版等，可以让代码更易阅读、更具美感。

**单行注释**

必须独占一行。`//` 后跟一个空格，缩进与下一行被注释说明的代码一致。

**多行注释**

避免使用 `/*...*/` 这样的多行注释。有多行注释内容时，使用多个单行注释。

**函数/方法注释**
1. 函数/方法注释必须包含函数说明，有参数和返回值时必须使用注释标识。；
2. 参数和返回值注释必须包含类型信息和说明；
3. 当函数是内部函数，外部不可访问时，可以使用 @inner 标识；

```javascript
/**
 * 函数描述
 *
 * @param {string} p1 参数1的说明
 * @param {string} p2 参数2的说明
 * @param {number=} p3 参数3的说明（可选）
 * @return {Object} 返回值描述
 */
function foo(p1, p2, p3) {
    var p3 = p3 || 10;
    return {
        p1: p1,
        p2: p2,
        p3: p3
    };
}
```

**文件注释**

文件注释用于告诉不熟悉这段代码的读者这个文件中包含哪些东西。 应该提供文件的大体内容, 它的作者, 依赖关系和兼容性信息。如下:

```javascript
/**
 * @fileoverview Description of file, its uses and information
 * about its dependencies.
 * @author xxx@hand-china.com
 * Copyright 2015 xxx Inc. All Rights Reserved.
 */
```

#### 命名

**变量**, 使用 Camel 命名法。

```javascript
var loadingModules = {};
```

**私有属性、变量和方法**以下划线 _ 开头。
```javascript
var _privateMethod = {};
```

**常量**, 使用全部字母大写，单词间下划线分隔的命名方式。

```javascript
var HTML_ENTITY = {};
```

1. **函数**, 使用 Camel 命名法。
2. 函数的**参数**, 使用 Camel 命名法。

```javascript
function stringFormat(source) {}

function hear(theBells) {}
```

1. **类**, 使用 Pascal 命名法
2. 类的 **方法 / 属性**, 使用 Camel 命名法

```javascript
function TextNode(value, engine) {
    this.value = value;
    this.engine = engine;
}

TextNode.prototype.clone = function () {
    return this;
};
```

1. **枚举变量** 使用 Pascal 命名法。
2. **枚举的属性**， 使用全部字母大写，单词间下划线分隔的命名方式。

```javascript
var TargetState = {
    READING: 1,
    READED: 2,
    APPLIED: 3,
    READY: 4
};
```

由多个单词组成的 **缩写词**，在命名中，根据当前命名法和出现的位置，所有字母的大小写与首字母的大小写保持一致。

```javascript
function XMLParser() {}

function insertHTML(element, html) {}

var httpRequest = new HTTPRequest();
```

#### 命名语法
**类名**，使用名词。

```javascript
function Engine(options) {}
```

**函数名**，使用动宾短语。

```javascript
function getStyle(element) {}
```

**boolean** 类型的变量使用 is 或 has 开头。

```javascript
var isReady = false;
var hasMoreCommands = false;
```

**Promise 对象**用动宾短语的进行时表达。

```javascript
var loadingData = ajax.get('url');
loadingData.then(callback);
```

#### 接口命名规范

1. 可读性强，见名晓义；
2. 尽量不与 jQuery 社区已有的习惯冲突；
3. 尽量写全。不用缩写，除非是下面列表中约定的；（变量以表达清楚为目标，uglify 会完成压缩体积工作）

| 常用词 | 说明 |
| -- | -- |
| options | 表示选项，与 jQuery 社区保持一致，不要用 config, opts 等 |
| active | 表示当前，不要用 current 等 |
| index | 表示索引，不要用 idx 等 |
| trigger | 触点元素 |
| triggerType | 触发类型、方式 |
| context | 表示传入的 this 对象 |
| object | 推荐写全，不推荐简写为 o, obj 等 |
| element | 推荐写全，不推荐简写为 el, elem 等 |
| length | 不要写成 len, l |
| prev | previous 的缩写 |
| next | next 下一个 |
| constructor |	不能写成 ctor |
| easing | 示动画平滑函数 |
| min |	minimize 的缩写 |
| max |	maximize 的缩写 |
| DOM |	不要写成 dom, Dom |
| .hbs | 使用 hbs 后缀表示模版 |
| btn |	button 的缩写 |
| link| 超链接 |
| title	| 主要文本 |
| img | 图片路径（img标签src属性）|
| dataset |	html5 data-xxx 数据接口 |
| theme | 主题 |
| className	| 类名 |
| classNameSpace | class 命名空间 |

#### True 和 False 布尔表达式
类型检测优先使用 typeof。对象类型检测使用 instanceof。null 或 undefined 的检测使用 == null。

下面的布尔表达式都返回 false:

* null
* undefined
* '' 空字符串
* 0 数字0

但小心下面的, 可都返回 true:

* '0' 字符串0
* [] 空数组
* {} 空对象

#### 不要在 Array 上使用 for-in 循环
for-in 循环只用于 `object/map/hash` 的遍历, 对 `Array` 用 for-in 循环有时会出错. 因为它并不是从 0 到 length - 1 进行遍历, 而是所有出现在对象及其原型链的键值。
```javascript
// Not recommended
function printArray(arr) {
  for (var key in arr) {
    print(arr[key]);
  }
}

printArray([0,1,2,3]);  // This works.

var a = new Array(10);
printArray(a);  // This is wrong.

a = document.getElementsByTagName('*');
printArray(a);  // This is wrong.

a = [0,1,2,3];
a.buhu = 'wine';
printArray(a);  // This is wrong again.

a = new Array;
a[3] = 3;
printArray(a);  // This is wrong again.

// Recommended
function printArray(arr) {
  var l = arr.length;
  for (var i = 0; i < l; i++) {
    print(arr[i]);
  }
}
```

#### 二元和三元操作符
操作符始终写在前一行, 以免**分号的隐式插入**产生预想不到的问题。

```javascript
var x = a ? b : c;

var y = a ?
    longButSimpleOperandB : longButSimpleOperandC;

var z = a ?
        moreComplicatedB :
        moreComplicatedC;
```

`.` 操作符也是如此：

```javascript
var x = foo.bar().
    doSomething().
    doSomethingElse();
```

#### 条件(三元)操作符 (?:)
三元操作符用于替代 if 条件判断语句。

```javascript
// Not recommended
if (val != 0) {
  return foo();
} else {
  return bar();
}

// Recommended
return val ? foo() : bar();
```

#### && 和 ||
二元布尔操作符是可短路的, 只有在必要时才会计算到最后一项。

```javascript
// Not recommended
function foo(opt_win) {
  var win;
  if (opt_win) {
    win = opt_win;
  } else {
    win = window;
  }
  // ...
}

if (node) {
  if (node.kids) {
    if (node.kids[index]) {
      foo(node.kids[index]);
    }
  }
}

// Recommended
function foo(opt_win) {
  var win = opt_win || window;
  // ...
}

var kid = node && node.kids && node.kids[index];
if (kid) {
  foo(kid);
}
```
##<span id="jquery">jQuery </span>

#### 使用最新版本的 jQuery

最新版本的 jQuery 会改进性能和增加新功能，若不是为了兼容旧浏览器，建议使用最新版本的 jQuery。以下是三条常见的 jQuery 语句，版本越新，性能越好：
```javascript
$('.elem')
$('.elem', context)
context.find('.elem')
```

#### jQuery 变量
1. 存放 jQuery 对象的变量以 `$` 开头；
2. 将 jQuery 选择器返回的对象缓存到本地变量中复用；
3. 使用驼峰命名变量；

```javascript
var $myDiv = $("#myDiv");
$myDiv.click(function(){...});
```

#### 选择器
1. 尽可能的使用 ID 选择器，因为它会调用浏览器原生方法 `document.getElementById` 查找元素。当然直接使用原生 `document.getElementById` 方法性能会更好；
2. 在父元素中选择子元素使用 `.find()` 方法性能会更好, 因为 ID 选择器没有使用到 Sizzle 选择器引擎来查找元素；

```javascript
// Not recommended
var $productIds = $("#products .class");

// Recommended
var $productIds = $("#products").find(".class");
```

#### DOM 操作
1. 当要操作 DOM 元素的时候，尽量将其分离节点，操作结束后，再插入节点；
2. 使用字符串连接或 `array.join` 要比 `.append()`性能更好；

```javascript
var $myList = $("#list-container > ul").detach();
//...a lot of complicated things on $myList
$myList.appendTo("#list-container");
```

```javascript
// Not recommended
var $myList = $("#list");
for(var i = 0; i < 10000; i++){
    $myList.append("<li>"+i+"</li>");
}

// Recommended
var $myList = $("#list");
var list = "";
for(var i = 0; i < 10000; i++){
    list += "<li>"+i+"</li>";
}
$myList.html(list);

// Much to recommended
var array = [];
for(var i = 0; i < 10000; i++){
    array[i] = "<li>"+i+"</li>";
}
$myList.html(array.join(''));
```

#### 事件
1. 如果需要，对事件使用自定义的 `namespace`，这样容易解绑特定的事件，而不会影响到此 DOM 元素的其他事件监听；
2. 对 Ajax 加载的 DOM 元素绑定事件时尽量使用事件委托。事件委托允许在父元素绑定事件，子代元素可以响应事件，也包括 Ajax 加载后添加的子代元素；

```javascript
$("#myLink").on("click.mySpecialClick", myEventHandler);
$("#myLink").unbind("click.mySpecialClick");
```

```javascript
// Not recommended
$("#list a").on("click", myClickHandler);

// Recommended
$("#list").on("click", "a", myClickHandler);
```

#### 链式写法
1. 尽量使用链式写法而不是用变量缓存或者多次调用选择器方法；
2. 当链式写法超过三次或者因为事件绑定变得复杂后，使用换行和缩进保持代码可读性；

```javascript
$("#myDiv").addClass("error").show();
```

```javascript
$("#myLink")
  .addClass("bold")
  .on("click", myClickHandler)
  .on("mouseover", myMouseOverHandler)
  .show();
```

#### 其他
1. 多个参数使用对象字面量存储；
2. 不要将 CSS 写在 jQuery 里面；
3. 正则表达式仅准用 .test() 和 .exec() 。不准用 "string".match() ；


### 性能优化
####避免不必要的 DOM 操作
浏览器遍历 DOM 元素的代价是昂贵的。最简单优化 DOM 树查询的方案是，当一个元素出现多次时，将它保存在一个变量中，就避免多次查询 DOM 树了。

```javascript
// Recommended
var myList = "";
var myListHTML = document.getElementById("myList").innerHTML;

for (var i = 0; i < 100; i++) {
  myList += "<span>" + i + "</span>";
}

myListHTML = myList;

// Not recommended
for (var i = 0; i < 100; i++) {
  document.getElementById("myList").innerHTML += "<span>" + i + "</span>";
}
```

####缓存数组长度
循环无疑是和 JavaScript 性能非常相关的一部分。通过存储数组的长度，可以有效避免每次循环重新计算。

注: 虽然现代浏览器引擎会自动优化这个过程，但是不要忘记还有旧的浏览器。
```javascript
var arr = new Array(1000),
    len, i;
// Recommended - size is calculated only 1 time and then stored
for (i = 0, len = arr.length; i < len; i++) {

}

// Not recommended - size needs to be recalculated 1000 times
for (i = 0; i < arr.length; i++) {

}
```

####异步加载第三方内容
当你无法保证嵌入第三方内容比如 Youtube 视频或者一个 like/tweet 按钮可以正常工作的时候，你需要考虑用异步加载这些代码，避免阻塞整个页面加载。

```javascript
(function() {

    var script,
        scripts = document.getElementsByTagName('script')[0];

    function load(url) {
      script = document.createElement('script');
      script.async = true;
      script.src = url;
      scripts.parentNode.insertBefore(script, scripts);
    }

    load('//apis.google.com/js/plusone.js');
    load('//platform.twitter.com/widgets.js');
    load('//s.widgetsite.com/widget.js');

}());
```

#### 避免使用 jQuery 实现动画
1. 禁止使用 slideUp/Down() fadeIn/fadeOut() 等方法；
2. 尽量不使用 animate() 方法；


#<span id="bootstrap">Bootstrap</span>
-------------------
####移动设备优先
为了确保正确的渲染和触摸缩放，需要在`head`之中添加`viewport meta`标签
```html
<meta name="viewport" content="width=device-width, initial-scale=1">
```
在`viewport`标签上添加user-scalable=no来禁用移动设备上的缩放功能
```html
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
```
####栅格系统
Bootstrap包括了一套响应式、移动设备优先的流式栅格系统，随着屏幕设备或视口（viewport）尺寸的增加，系统会自动扩展到最多12列。
>- 行（row）必须包含在 .container（固定宽度）或 .container-fluid（全屏宽度）中，以便自身调整或填充。
- 用行（row）来创建一组水平的列（column）。
- 内容应放在列（column）中， 只有列（column）可以是行（row）的直接子元素。
- 预定义的栅格类，像 .rowand .col-xs-4可用于快速创建栅格布局。Less mixins 也可以用来创建更具语义化的布局。
- 列通过设置 padding来创建列之间的间隔。然后通过在 .row上设置负值的 margin来抵消为第一和最后一列的 padding。
- 负值的 margin就是下面的示例为什么是向外突出的原因。在栅格列中的内容排成一行。
- 栅格系统中的列是通过指定1到12的值来表示其跨越的范围。例如，三个等宽的列可以使用三个 .col-xs-4来创建。
- 如果一行中超过12列， 各组额外列，将作为一个单元， 换到新的一行。
- 栅格类适用于与屏幕宽度大于或等于分界点大小的设备 ， 并且针对小型设备覆盖栅格类。 因此，在元素上应用任何 .col-md-类不仅会影响媒体设备，同时如果 .col-lg-不存在， 也影响大型设备。

#### Button
在`a`, `button`, 或`input`元素上使用button样式类
```html
<a class="btn btn-default" href="#" role="button">Link</a>
<button class="btn btn-default" type="submit">Button</button>
<input class="btn btn-default" type="button" value="Input">
<input class="btn btn-default" type="submit" value="Submit">
```
>建议使用 **`button`** 元素 ，以确保跨浏览器的渲染。
>
####响应式的图片
通过图片上添加`.img-responsive`样式类能友好的支持响应式自适应。 它应用max-width: 100%; 和 height: auto;到图片上，使其很好地根据父元素缩放。
```html
<img src="..." class="img-responsive" alt="Responsive image">
```
>Internet Explorer 8是不支持圆角的。

#<span id="project">项目规范</span>
-------------------
##项目结构
```
webapp                                  # webapp                               
|-- lib                                 # 第三方lib                                         
|-- resources                              
|   |-- css                             # css目录
|   |   |-- bootstrap.min.css
|   |   |-- font-awesome.min.css
|   |   |-- ...
|   |-- fonts                           # 字体目录
|   |   |-- fontawesome-webfont.ttf
|   |   |-- fontawesome-webfont.svg
|   |   |-- ...
|   |-- js                              # javascript目录
|   |   |-- bootstrap.min.js
|   |   |-- jquery.min.js
|   |   |-- ...
|   |-- images                          # image目录
|   |   |-- logo.png
|   |   |-- ...
|   |-- WEB-INF                         # web-inf
|   |   |-- web.xml
```
