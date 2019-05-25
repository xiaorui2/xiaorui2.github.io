---
title: Css语法熟悉
date: 2019-05-23 13:59:59
tags: Css
categories: 前端学习
---

# Css概念

它是一个层叠样式表，存储着很多的样式，而样式定义着如何显示`Html`元素，因此解决了内容与表现分离的问题，就是我`Html`只需要写内容，然后用`Css`来控制它的表现。

当同一个`Html`元素被不止一个样式定义的时候，会根据优先级高低控制，优先级从高到低依次是：

内联样式（在 `Html`元素内部）-> 内部样式表（位于 `<head>` 标签内部）->

外部样式表 -> 浏览器缺省设置

# Css基础

## Css语法

`CSS` 规则由两个主要的部分构成：选择器，以及一条或多条声明。

选择器通常是您需要改变样式的 `Html`元素。每条声明由一个属性和一个值组成。属性就是希望设置的样式属性。你也可以对选择器进行分组，如下：

```html
h1,h2,h3,h4,h5,h6 {
  color: green;
  }
```

## Css的选择器

### 派生选择器

用于根据文档的上下文关系来确定某个标签的样式，比方说希望列表中的` strong`元素变为斜体字，那么我选择器就写成`li strong {...}`，这样的话只有 `li `元素中的`strong`元素的样式为斜体字，不在`li`元素内部的就还是正常的粗体字.

### `id`选择器

`id` 选择器以` "#"` 来定义。可以独立的调用如：`#red {color:red;}`。它可以利用派生选择器的思想进行联合使用，比方：

```html
#sidebar p {
	font-style: italic;
	text-align: right;
	margin-top: 0.5em;
	}
```

那么上面的样式只会应用于出现在` id` 是 `sidebar` 的元素内的段落。

注意：`id `属性只能在每个 `Html` 文档中出现一次。

### 类选择器

它以一个点号显示：

```html
.center {text-align: center}
<h1 class="center">
This heading will be center-aligned
</h1>
```

注意：类名的第一个字符不能使用数字。

### 属性选择器

它以为拥有指定属性的` HTML` 元素设置样式，而不仅限于` class `和 `id `属性，对带有指定属性的` HTML` 元素设置样式。它还可以绑定值：

```html
<!-- 不带后面的等号内容的时候就是为所有带有 title 属性的所有元素设置样式 -->
[title=W3School]
{
border:5px solid blue;
}
```

属性选择器在为不带有 `class` 或` id `的表单设置样式时特别有用，如设置表单的样式

```html
<!DOCTYPE html>
<html>
<head>
<style>
input[type="text"]
{
  width:150px;
  display:block;
  margin-bottom:10px;
  background-color:yellow;
  font-family: Verdana, Arial;
}

input[type="button"]
{
  width:120px;
  margin-left:35px;
  display:block;
  font-family: Verdana, Arial;
}
</style>
</head>
<body>

<form name="input" action="" method="get">
<input type="text" name="Name" value="Bill" size="20">
<input type="text" name="Name" value="Gates" size="20">
<input type="button" value="Example Button">
</form>
    
</body>
</html>

```

## 如何插入一个样式表

### 外部样式表

个页面使用 `<link> `标签链接到样式表。`<link> `标签在（文档的）头部：

```html
<head>
<link rel="stylesheet" type="text/css" href="mystyle.css" />
</head>
```

### 内部样式表

当单个文档需要特殊的样式时，就应该使用内部样式表。你可以使用 `<style> `标签在文档头部定义内部样式表，就像这样:

```html
<head>
<style type="text/css">
  hr {color: sienna;}
  p {margin-left: 20px;}
  body {background-image: url("images/back40.gif");}
</style>
</head>
```

### 内联样式

当样式仅需要在一个元素上应用一次时。

```html
<p style="color: sienna; margin-left: 20px">
This is a paragraph
</p>
```

注意：如果某些属性在不同的样式表中被同样的选择器定义，那么属性值将从更具体的样式表中被继承过来。相同的时候按优先级来选择继承。