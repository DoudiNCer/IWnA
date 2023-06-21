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

