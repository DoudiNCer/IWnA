# HTML&CSS学习笔记

## 前端语言

&emsp;&emsp;前端作重要的三门语言为HTML、CSS与JS：

- HTML：Hyper Text Markup Language，超文本标记语言。HTML 定义了页面上的所有元素及其关系，是页面的骨架。
- CSS：Cascading Style Sheets，层叠样式表。定义了 HTML 各个元素的各种属性与风格
- JS：JavaScript，一门动态弱类型语言，用于自定义页面交互。

## HTML

&emsp;&emsp;HTML 是一门**标记语言**，而非编程语言，其支持的语法包括元素、文本、注释、DTD 与处理信息：

- 标签（元素，Elemrnt）：包括闭合元素（如`<div> </div>`）与非闭合元素（如`<p>`）
- 文本（Text）：可以是单纯的文本或`<![CDATA[text]]>`
- 注释（Comment）：如`<!-- 这是注释 -->`
- DTD：Document Type Definition，用于定义文档类型

&emsp;&emsp;一个 HTML 文档的大致结构如下：

```html
<!-- 这是注释 -->
<!--
  这也是注释
  -->
<!-- 告诉浏览器这是 HTML5 文档 -->
<!DOCTYPE html>
<html>
     <!-- HTML头元素，用于定义页面的一些属性 -->
    <head>
        <meta charset="utf-8">
        <title>My HTML Document</title>
         <!-- 内嵌CSS -->
        <style>
            
        </style>
    </head>
    <!-- HTML体元素，定义页面中的内容 -->
    <body>
        
    </body>
</html>
```

### HTML元素

&emsp;&emsp;HTML的元素名写在“<>”中。闭合元素使用一对标签名包裹元素内容，在起始标签内设置属性；非闭合元素无结束标签。如：

```html
<h1>这是一个标题</h1>
<a href="https://code.sipcoj.com">这是一个好康的链接</a>a
<img src="https://github.com/account" alt= "这是一张猫猫图">
```

> 0. 在 [MDN Web Docs]( https://developer.mozilla.org/en-US/docs/web/html/element ) 可查询到所有 HTML 元素及其用法
> 0. 元素的内容可以是其他元素，也可以是上述 HTML语法中的任何一种

#### 头部元素

&emsp;&emsp;`<head></head>`内的元素定义了页面的一些属性，比如用于指定页面标题的`<title></title>`、引入内嵌 CSS 的`<style></style>`、引入内嵌 JavaScript 的`<script></script>`和设置页面其他属性的`<meta>`：

```html
<!-- 定义⽂档关键词，⽤于搜索引擎 -->
<meta name="keywords" content="HTML, CSS, XML, XHTML, JavaScript">
```

```html
<!-- 定义web⻚⾯描述 -->
<meta name="description" content="网页简述">
```

```html
<!-- 定义⻚⾯作者 -->
<meta name="author" content="猫猫，xxx@cat.xyz">
```

```html
<!-- Copyright -版权 -->
<meta name=“Copyright” Content=“XX公司">
```

```html
<!-- content-Language -显示字符集的设定 -->
<meta http-equiv="Content-Language" Content="zh-CN">
```

```html
<!--content-Type-定义⽹络⽂件的类型和⽹⻚的编码 -->
<meta http-equiv="Content-Type" Content="text/html; Charset=utf-8">
```

```html
<!-- ⾃动更新⻚⾯每20秒 -->
<meta http-equiv="refresh" content="30">
```

#### 体元素

&emsp;&emsp;在`<body></body>`中的元素定义了页面上的内容，如各种等级的标题`<h1></h1>`、分段`<p></p>`、表格`<table></table>`等。

&emsp;&emsp;有的元素默认情况下在页面中占据一整行，其大小可以自由调整，称为块级元素（`block`）；有的元素默认不会占据整行，其大小取决于其内容，不可自由调整，称为内联元素（`inline`）有的元素默认不占据一整行，但其大小可自由调整，称为内联块（`inline-block`）。

```html
<!-- 
    块级元素总是在新行上开始并占据一整行 
    其高度、宽度可使用 CSS 强制指定，即使子元素大小大于父元素，父元素大小也不会受到影响。
    行高、内外边距均可使用 CSS 控制。
-->
<html>
    <head>
        <title>块级元素</title>
    </head>
    <body>
        <div>
            我是 div，用于配合 CSS 实现复杂的效果
        </div>
        <p>
            我是p
        </p>
        <!-- hr 会插入一个空行 -->
        <hr>
        <h1 align="center"> 1级标题 </h1>
        <h2 align="right"> 2级标题 </h2>
        <h3 style="height: 50px;width: 100px;"> 3级标题 </h3>
    </body>
</html>
```

```html
<!-- 
    内联元素又称为“文本元素”，在一行内按照从左到右的顺序显示
    其高度和宽度取决于其内容，无法使用 CSS 进行设置
    内外编剧也不可以使用 CSS 进行控制
-->
<html>
    <head>
        <title>内联元素</title>
    </head>
    <body>
        <span>span元素,</span>
        <b>b元素，</b>
        <i>i元素，</i>
        <a>a元素</a>
        <br/>
        <i>i元素，</i>
        <hr/>
    </body>
</html>
```

```html
<!--
    内联块也显示在一行中，但其大小和内外边距可以由 CSS强制控制
-->
<html>
    <head>
        <title>内联块</title>
    </head>
    <body>
        <input type="text"/>
        <select>
            <option>1</option>
            <option>2</option>
        </select>
    </body>
</html>
```

> 0. 注意“默认”，可使用 CSS 中的 `display` 属性重新定义元素显示方式

### 文本、图片与超链接

#### 内联文本

&emsp;&emsp;以下是一些默认情况下为内联元素的文本特殊效果：

- `<u></u>`：下划线文本

    <u>我是下划线文本</u>

- `<strong></strong>`：加粗文本

    <strong>我是加粗文本</strong>

- `<em></em>`：斜体文本

    <em>我是斜体文本</em>

#### 块级文本

&emsp;&emsp;一至六级标题属于块级元素，占据整行。

#### 超链接

&emsp;&emsp;超链接使用`<a>`元素编写，属于内联元素：

```html
<a href="https://code.sipcoj.com"> 好康的 </a>
<!--
    href：链接指向的地址，可以是绝对引用、相对引用或锚点（anchor，即使用“#”调用指定元素的ID）
    target：打开链接的方式，如
        在新窗口打开（_blank）
        相同框架内打开（_self，默认）
        在父框架内打开（_parent）
        在整个框架内打开（_top）
		在指定框架内打开
-->
```

#### 图片

&emsp;&emsp;使用`<img>`标签插入图片，属于内联块：

```html
<img src="https://github.com/account" alt="猫猫图" align="“middle”">
```

### 表格

&emsp;&emsp;HTML 中一个表格使用`<table></table>`定义，其中表头使用`<thead></thead>`定义，表体使用`<tbody></tbody>`定义。如：

```html
<table>
    <thead>
        <tr>
            <th>ID</th>
            <th>Name</th>
            <th>Age</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>1</td>
            <td>猫猫</td>
            <td>20</td>
        </tr>
        <tr>
            <td>2</td>
            <td>狗狗</td>
            <td>20</td>
        </tr>
        <tr>
            <td>3</td>
            <td>鼠鼠</td>
            <td>19</td>
        </tr>
    </tbody>
</table>
```

&emsp;&emsp;表格可以使用以下可选属性

- border：边框粗细，值为数字
- background：背景图片
- bgcolor：背景颜色
- width、height：宽度高度
- align：表格的对齐方式，包括左对齐（`left`）、居中（`center`）、右对齐（`right`）
- valign：表格内容对齐方式
- cellpadding：单元格之间的距离
- cellspacing：单元格内容与边框的距离

### 表单

&emsp;&emsp;表单用于接收用户的输入并将其发送到后端服务器，并接收服务器的处理结果，表单使用`<form></form>`标签定义。`<form></form>`标签的私有属性包括：

- action：请求要发送到的地址
- method：请求方式，只能使用 GET 或 POST
- target：在何处提交表单
- enctype：对表单数据进行编码，适用于 POST 请求

&emsp;&emsp;表单中用于接收用户输入的方式有以下几种：

- input：多功能输入框，根据`type`属性有多种表现形式，如复选框（`checkbox`）、文件选择（`file`）、密码框（`password`）、单选（`radio`）、文本框（`text`）等。如：

    ```html
    <div>
        <label for "username">用户名</label>
        <input id="username" type="text" name="username">
    </div>
    <div>
        <label>性别：</label>
        <input type="radio" name="gender" value="男"/>男
        <input type="radio" name="gender" value="⼥"/>⼥
    </div>
    <div>
        <input type="submit" value="提交按钮"/>
        <input type="button" value="按钮"/>
        <input type="reset" value="重置按钮"/>
     </div>
    ```

- select/option：下拉框，用于多选一，如：

    ```html
    <div>
        <label>城市：</label>
        <select name="city">
            <option>--请选择--</option>
            <option value="天津">天津</option>
            <option value="北京">北京</option>
        </select>
     </div>
    ```

    &emsp;&emsp;表单的内容有以下几种通用属性：

    - name：用于指定该输入在表单中的属性名
    - disabled：警用该元素
    - readonly：该输入不可输入
    - value：选择该选项对应的属性值

### 列表

&emsp;&emsp;列表分为有序列表、无序列表和自定义列表，其中有序列表使用`<ol></ol>`标签，其中每一项使用`<li></li>`，如：

```html
<div>
    <!-- type 可以是 A、a、1、i、l -->
    <ol type=“1”>
        <li>猫猫</li>
        <li>狗狗</li>
        <li>鼠鼠</li>
    </ol>
</div>
```

&emsp;&emsp;无序列表使用`<ul></ul>`标签，其 type 可以使用空心圆（`circle`）、实心圆（`disc`）、正方形（`square`）。

&emsp;&emsp;自定义列表使用`<dl></dl>`标签，如：

```html
<dl>
    <dt>个⼈中⼼</dt>
    <dd>我的资料</dd>
    <dt>⾸⻚</dt>
    <dd>轮播图</dd>
 </dl>
```

## CSS

&emsp;&emsp;CSS（Cascading Style Sheets，层叠样式表）是用于更灵活地定义 HTML 元素样式的一种语言，实现了元素与样式的分离。在以前，元素的样式写在元素的`style`属性中（内联样式，Inline style），可复用性较低且代码格式混乱。现在，我们可以使用 CSS 统一定义元素的样式。CSS 可以写在 HTML文档 头元素的`<style></style>`元素中（内部样式表，Internal style sheet），也可写在单独的 css 文件中并在头元素中使用`<link rel="stylesheet" type="text/css" href="mystyle.css">`引入（外部样式表，External style sheet）。

&emsp;&emsp;每条 CSS 规则由选择器和一个或多个声明组成，如：

```css
/* 我是注释 */
h1 {color: blue; font-size: 12px}
```

### 选择器

&emsp;&emsp;选择器用于选择 CSS 规则作用的元素，可分为基础选择器、复合选择器和伪类/伪元素选择器。

#### 基础选择器

&emsp;&emsp;基础选择器包括标签选择器、类（Class）选择器和ID选择器：

&emsp;&emsp;标签选择器可以选中某种标签，选择器为`标签名`。如：

```html
<html>
    <head>
        <title>标签选择器</title>
        <style>
            h1 {color: red}
        </style>
    </head>
    <body>
        <h1>标题</h1>
        <h1>标题</h1>
        <h1>标题</h1>
    </body>
</html>
```

&emsp;&emsp;类（Class）选择器会选中所有`class`属性相同的元素，选择器为`.类名`。如：

```html
<html>
    <head>
        <title>类选择器</title>
        <style>
            .clazz {color: red}
        </style>
    </head>
    <body>
        <div class=“clazz”>喵</div>
        <div class=“clazz”>喵喵</div>
        <div >喵喵喵</div>
    </body>
</html>
```

&emsp;&emsp;ID 选择器可以选中某 ID （唯一）的元素，选择器为`#ID`。如：

```html
<html>
    <head>
        <title>ID</title>
        <style>
            #clazz {color: red}
        </style>
    </head>
    <body>
        <div id=“clazz”>喵</div>
        <div>喵喵</div>
    </body>
</html>
```

#### 复合选择器

&emsp;&emsp;复合选择器是通过基础选择器组合的选择器，包括并集选择器、后代选择器与交集选择器。

&emsp;&emsp;并集选择器由多个基础选择器通过`,`连接，选中满足任一条件的所有元素，如：

```css
.photo, #mian{margin 0}
```

&emsp;&emsp;后代选择器由从选中父元素的选择器到选中子元素的选择器通过` `连接而成，的用于选中满足某种继承关系的元素。如：

```css
body #mian .photo{margin: 0
```

&emsp;&emsp;交集选择器以标签选择器开头，后接类选择器或ID选择器，选中该类或该ID的该标签。如：

```css
div.photo{margin: 0}
```

#### 伪类选择器

&emsp;&emsp;伪类是添加到选择器的关键字，用于指定要选择的元素的**特殊状态**。以`:`开始。可以在⼀个选择器中同时⼀起写多个伪类。一些通用伪类如：

> - `:first-child`：选择某个元素的第⼀个⼦元素； 
> - `:last-child`：选择某个元素的最后⼀个⼦元素；
> - `:nth-child(n)`：匹配属于其⽗元素的第 n个⼦元素，不论元素的类型；
> - `:nth-last-child()`：从这个元素的最后⼀个⼦元素开始算，选匹配属于其⽗元素的第 n个⼦元素，不论元素的类型；
> - `:nth-of-type()`：规定属于其⽗元素的第n个 指定 元素；
> - `:nth-last-of-type()`：从元素的最后⼀个开始计算,规定属于其⽗元素的指定 元素；
> - `:first-of-t、ype`：选择⼀个上级元素下的第⼀个同类⼦元素；

#### 伪元素选择器

&emsp;&emsp;&emsp;&emsp;伪元素是是⼀个附加⾄选择器末的关键词，允许对被选择元素的**特定部分**修改样式。伪元素以`::`开头，一些通用的伪元素如：

> - ` ::after`
> - `::before`
> - `::first-letter`：选择⾸⾏⾸字⺟
> - `::first-line`：选择⾸⾏

### 背景

&emsp;&emsp;cdefHTML 元素的背景可以使用颜色或图片，相关属性包括：

> - `background-color`：背景颜色，可使用六位十六进制颜色（`#ff0000`）、颜色名（`red`）或RGB（`RGB(255, 0, 0)`）
> - `background-image`：背景图片，使用`url("/img/background.jpg")`设置图片
> - `background-repeat`：图片对齐方式，默认图片会重复平铺，使用该属性可设置只在水平方向重复平铺（`repeat-x`）、只在竖直方向重复平铺（`repeat-y`）或不重复平铺（`no-repeat`）
> - `background-attachment`：图像是否固定
> - `background-position`：图像对齐方式

&emsp;&emsp;也可使用`background`属性简化上述配置，根据上述顺序依次给出属性值。

### 文本与链接

&emsp;&emsp;文本可用的属性包括颜色（`color`）、对齐方式（`text-align`）、大小写转换（`text-transform`）、文本缩进（`text-indent`）等。

&emsp;&emsp;链接可使用背景、颜色、字体等属性。我们可以使用以下几种伪类选择器设置不同状态下的链接的样式：

> - `a:link`：未访问的链接
> - `a:visited`：访问过的链接
> - `a:hover`：掠过（鼠标触碰到）的链接
> - `a:active`：激活（鼠标点击时）的链接

### 盒子模型

&emsp;&emsp;可将所有 HTML 元素看成一个盒子（Box），每个盒子具有 Margin（外边距）、Border（边框）、Padding（内边距）和 Content（内容）。

&emsp;&emsp;Margin 和 Padding 是透明的，可使用`margin`和`padding`属性指定其宽度。参数为四个、两个或一个参数，顺序为上右下左、上（下）右（左）或全部，值可使用`auto`、具体的宽度（px）或百分比。

&emsp;&emsp;元素的边框（Border）可以设置样式（`border-style`），如虚线边框（`dashed`）、实线边框（`solid`）、3D嵌入边框（`inset`）等。

&emsp;&emsp;HTML中的元素可通过`display`改变展示方式（块级元素`block`、内联元素`inline`、内联块`inline-block`、不展示`none`）

> 0. `display:none`可以完全隐蔽一个元素；`visibility:hidden`可以不显示该元素的内容但占据原本大小的空间

&emsp;&emsp;`position`可改变元素的定位方式：

- `static`：默认值，不会受到 `top`、`bottom`、 `left`、`right`的影响
- `fixed`：元素位置相对浏览器固定
- `relative`：元素位置相对其“正常”位置有偏差
- `absolute`：元素位置相对于其附近固定元素固定
- `sticky`：粘性定位，依赖于用户滚动

### Flex 布局

&emsp;&emsp;Flex（Flexible Box）是 CSS3 引入的一种布局方式。采⽤ Flex 布局的元素，称为 Flex 容器（Flex Container），其子元素自动转化为 Flex 元素（Flex Item）。

&emsp;&emsp;使用`display:flex`或`display:inline-flex`将一个元素转化为 Flex 容器，此时其子元素受 Flex 布局影响但其子元素的子元素不受影响。

&emsp;&emsp;在 Flex Container 中有两条轴，分别为水平的主轴（main axis）与竖直的交叉轴（cross axis）。项目默认按主轴排列，可使用`flex-direction`属性修改。

&emsp;&emsp;Flex Container/Item 的其他属性如下：

| 属性            | 描述                                                         |
| --------------- | ------------------------------------------------------------ |
| display         | 指定 HTML 元素的盒子类型                                     |
| flex-direction  | 指定弹性盒子中子元素的排列方式                               |
| flex-wrap       | 设置当弹性盒子的子元素超出父容器时是否换行                   |
| flex-flow       | flex-direction 和 flex-wrap 两个属性的简写                   |
| justify-content | 设置弹性盒子中元素在主轴（横轴）方向上的对齐方式             |
| align-items     | 设置弹性盒子中元素在侧轴（纵轴）方向上的对齐方式             |
| align-content   | 修改 flex-wrap 属性的行为，类似 align-items，但不是设置子元素对齐，而是设置行对齐 |
| order           | 设置弹性盒子中子元素的排列顺序                               |
| align-self      | 在弹性盒子的子元素上使用，用来覆盖容器的 align-items 属性    |
| flex            | 设置弹性盒子中子元素如何分配空间                             |
| flex-grow       | 设置弹性盒子的扩展比率                                       |
| flex-shrink     | 设置弹性盒子的收缩比率                                       |
| flex-basis      | 设置弹性盒子伸缩基准值                                       |
