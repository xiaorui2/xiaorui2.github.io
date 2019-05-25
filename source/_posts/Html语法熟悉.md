---
title: Html语法熟悉
date: 2019-05-23 13:59:44
tags: Html
categories: 前端学习
---

# Html基础

## `Html` 标题

`Heading`是通过 `<h1> - <h6>` 等标签进行定义的

```html
<h1>This is a heading</h1>
<h2>This is a heading</h2>
```

## `Html`段落

它是通过 `<p> `标签进行定义的

```html
<p>This is a paragraph.</p>
```

## `Html`折行

通过`<br/>`实现折行

```html
<p>This is<br />a para<br />graph with line breaks</p>
```

## `Html`水平线

是通过`<hr/>`用于在页面中创建水平线

```html
<p>This is a paragraph.</p>
<hr/>
```

## `Html`链接

```html
<a href="http://www.w3school.com.cn">This is a link</a>
```

## `Html`图像

通过` <img> `标签进行定义的，可以定义它的大小，位置等，还有一个代替文本

```html
<img src="w3school.jpg" width="104" height="142" alt="Big Boat">
```

## `Html`元素

是从开始标签到结束标签的所有代码。

```html
<html>
<body>
<p>This is my first paragraph.</p>
</body>
</html>
```

以上实例就是有三个元素，`<html> `元素，`<body>` 元素，`<p> `元素。对应的元素内容就是`body`元素，`p`元素和`This is my first paragraph`。

## `Html`属性

可以给`Html`元素提供更多的信息，并且属性总是在 `Html `元素的开始标签中规定。比方说它可以定义这个元素在哪个位置呈现，背景颜色是什么，是否加个链接，样式是什么这些的。列一点：

| **属性** | 值                 | **描述**                                 |
| -------- | ------------------ | ---------------------------------------- |
| class    | *classname*        | 规定元素的类名（classname）              |
| id       | *id*               | 规定元素的唯一 id                        |
| style    | *style_definition* | 规定元素的行内样式（inline style）       |
| title    | *text*             | 规定元素的额外信息（可在工具提示中显示） |

注意`Html`的`style`属性可以直接使用 `style` 属性直接将样式添加到 `HTML `元素，也可以间接地在独立的样式表中（`CSS` 文件）进行定义。比方说设置一下字体，大小，颜色等：

```
<html>
<body>
<p style="font-family:arial;color:red;font-size:20px;">A paragraph.</p>
</body>
</html>
```

## `Html`文本格式化对应标签：

| 标签       | 描述           |
| ---------- | -------------- |
| `<b>`      | 定义粗体文本。 |
| `<big>`    | 定义大号字。   |
| `<i>`      | 定义斜体字。   |
| `<small>`  | 定义小号字。   |
| `<strong>` | 定义加重语气。 |
| `<sub>`    | 定义下标字。   |
| `<sup>`    | 定义上标字。   |
| `<ins>`    | 定义插入字。   |

## `Html`引用

是通过`<q>`元素定义短的引用， `<blockquote> `元素定义被引用的节且会进行缩进处理。

## `Html`缩进

利用`<abbr>` 。

## `Html<address>` 

元素定义文档或文章的联系信息（作者/拥有者）。且此元素通常以斜体显示。大多数浏览器会在此元素前后添加折行。

## `Html`的计算机代码格式

对于计算机代码格式利用`<kbd>`（定义键盘输入）, `<samp>`（定义计算机输出示例）, `<pre>`（定义预格式化文本）以及 `<code>`（定义计算机代码文本）元素，如：

```html
<p>Coding Example:</p>
<code>
<pre>
var person = {
    firstName:"Bill",
    lastName:"Gates",
    age:50,
    eyeColor:"blue"
}
</pre>
</code>
```

## `Html`链接

它的`name`属性，它规定了锚的名称，当使用命名锚时，我们可以创建直接跳至该命名锚（比如页面中某个小节）的链接。如：

```html
首先，我们在 HTML 文档中对锚进行命名（创建一个书签）：
<a name="tips">基本的注意事项 - 有用的提示</a>
然后，我们可以在同一个文档中创建指向该锚的链接：
<a href="#tips">有用的提示</a>
也可以在其他页面中创建指向该锚的链接
<a href="http://www.w3school.com.cn/html/html_links.asp#tips">有用的提示</a>
```

## `Html`列表：

| 标签   | 描述           |
| ------ | -------------- |
| `<ol>` | 定义有序列表。 |
| `<li>` | 定义列表项。   |
| `<ul>` | 定义无序列表。 |
| `<dl>` | 自定义列表     |
| `<dt>` | 自定义项目     |
| `<dd>` | 自定义描述     |

## `Html`的`<div>`元素

它是一个块级元素(块级元素在浏览器显示时，通常会以新行来开始（和结束）)可用于组合其他`Html`元素的容器。

## `Html`的类和布局

对 HTML 进行分类（设置类），使我们能够为元素的类定义 CSS 样式。为相同的类设置相同的样式，或者为不同的类设置不同的样式。联合`<div>`理解，`<div>`把这个html分块了，有的块样式相同有的不相同就可以使用相同的类或者不同的类，如：

```html
<!DOCTYPE html>
<html>
<head>
<style>
.cities {
    background-color:black;
    color:white;
    margin:20px;
    padding:20px;
} 
</style>
</head>
<body>

<div class="cities">
<h2>London</h2>
<p>London is the capital city of England. 
It is the most populous city in the United Kingdom, 
with a metropolitan area of over 13 million inhabitants.</p>
</div>

<div class="cities">
<h2>Paris</h2>
<p>Paris is the capital and most populous city of France.</p>
</div>

<div class="cities">
<h2>Tokyo</h2>
<p>Tokyo is the capital of Japan, the center of the Greater Tokyo Area,
and the most populous metropolitan area in the world.</p>
</div>

</body>
</html>
```

同理`Html`的 ` <span>` 元素是行内元素，能够用作文本的容器。设置 `<span> `元素的类，能够为相同的` <span> `元素设置相同的样式。

布局的话就是`Html`和`Css`的联合使用了，利用`Html`语言对我们需要呈现的内容就行分块，然后`Css`对于这些分好的块填充样式，包括放哪。这样的结合之后就可以写出一个静态的页面了。

