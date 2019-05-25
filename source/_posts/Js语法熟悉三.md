---
title: Js语法熟悉三
date: 2019-05-24 09:46:38
tags: Js
categories: 前端学习
---

# JavaScript的异步处理

## Ajax

### Ajax概念与工作流程

`AJAX` 并非编程语言，它通过浏览器内建的 `XMLHttpRequest` 对象（从 `web` 服务器请求数据），然后利用`JavaScript` 和 `HTML DOM`（显示或使用数据）。它能够不刷新页面更新网页，在页面加载后从服务器请求和接收数据，在后台向服务器发送数据。

它的工作流程：网页中发生一个事件（页面加载、按钮点击） ->  

 `JavaScript` 创建 `XMLHttpRequest` 对象	->

`XMLHttpRequest` 对象向 `web` 服务器发送请求	->

服务器处理该请求	->

服务器将响应发送回网页	->

`JavaScript`读取响应并执行正确的动作（比如更新页面）

注意：AJAX请求是异步执行的，也就是说，要通过回调函数获得响应。

### Ajax实例

```html
<!DOCTYPE html>
<html>
<style>
table,th,td {
  border : 1px solid black;
  border-collapse: collapse;
}
th,td {
  padding: 5px;
}
</style>
<body>

<button type="button" onclick="loadDoc()">获取我的音乐列表</button>
<br><br>
<table id="demo"></table>

<script>
function loadDoc() {
  var xhttp = new XMLHttpRequest();
  xhttp.onreadystatechange = function() {
    if (this.readyState == 4 && this.status == 200) {
      myFunction(this);
    }
  };
  xhttp.open("GET", "/demo/music_list.xml", true);
  xhttp.send();
}
function myFunction(xml) {
  var i;
  var xmlDoc = xml.responseXML;
  var table="<tr><th>艺术家</th><th>曲目</th></tr>";
  var x = xmlDoc.getElementsByTagName("TRACK");
  for (i = 0; i <x.length; i++) { 
    table += "<tr><td>" +
    x[i].getElementsByTagName("ARTIST")[0].childNodes[0].nodeValue +
    "</td><td>" +
    x[i].getElementsByTagName("TITLE")[0].childNodes[0].nodeValue +
    "</td></tr>";
  }
  document.getElementById("demo").innerHTML = table;
}
</script>
</body>
</html>
```

`XMLHttpRequest`对象方法和属性

| 方法                                  | 描述                                                         |
| ------------------------------------- | :----------------------------------------------------------- |
| `new XMLHttpRequest()`                | 创建新的 `XMLHttpRequest` 对象                               |
| `getAllResponseHeaders()`             | 返回头部信息                                                 |
| `open(method, url, async, user, psw)` | 规定请求<br />`method`：请求类型 `GET` 或 `POST`<br />`url`：文件位置，使用的是相对路径<br />`async`：`true`（异步）或 `false`（同步）<br />`user`：可选的用户名称<br />`psw`：可选的密码 |
| `send()`                              | 将请求发送到服务器，用于 `GET` 请求                          |
| `send(string)`                        | 将请求发送到服务器，用于 `POST` 请求                         |
| `onreadystatechange`                  | 定义当 `readyState` 属性发生变化时被调用的函数               |
| `readyState`                          | 保存 `XMLHttpRequest` 的状态。<br />0：请求未初始化<br />1：服务器连接已建立<br />2：请求已收到<br />3：正在处理请求<br />4：请求已完成且响应已就绪 |
| `responseText/XML`                    | 以字符串/XML返回响应数据                                     |
| `status`                              | 返回请求的状态号：`200: "OK"`<br />`403: "Forbidden"`<br />`404: "Not Found"` |

## CORS跨域

在Ajax上我们知道输入URL使用的是相对路径，这是浏览器的同源策略导致的，也就是在发送AJAX请求时，URL的域名必须和当前页面完全一致，不然会报错。这也就是跨域

`Origin`表示本域，也就是浏览器当前页面的域。当`JavaScript`向外域（如`sina.com`）发起请求后，浏览器收到响应后，首先检查`Access-Control-Allow-Origin`是否包含本域，如果是，则此次跨域请求成功，如果不是，则请求失败，`JavaScript`将无法获取到响应的任何数据。假设本域是`my.com`，外域是`sina.com`，只要响应头`Access-Control-Allow-Origin`为`http://my.com`，或者是`*`，本次请求就可以成功。可见，跨域能否成功，取决于对方服务器是否愿意给你设置一个正确的`Access-Control-Allow-Origin`，决定权始终在对方手中。

![](1.png)

## Promise

在JavaScript的世界中，所有代码都是单线程执行的。

由于这个“缺陷”，导致JavaScript的所有网络操作，浏览器事件，都必须是异步执行。异步执行可以用回调函数实现，上面的Ajax就是典型的异步操作。

Promise是另一种异步的操作

它的处理想法就是先统一执行AJAX逻辑，不关心如何处理结果，然后，根据结果是成功还是失败，在将来的某个时候调用`success`函数或`fail`函数。

```javascript
function test(resolve, reject) {
    var timeOut = Math.random() * 2;
    log('set timeout to: ' + timeOut + ' seconds.');
    setTimeout(function () {
        if (timeOut < 1) {
            log('call resolve()...');
            resolve('200 OK');
        }
        else {
            log('call reject()...');
            reject('timeout in ' + timeOut + ' seconds.');
        }
    }, timeOut * 1000);
}
var p1 = new Promise(test);
//变量p1是一个Promise对象，它负责执行test函数。由于test函数在内部是异步执行的，当test函数执行成功时，我们告诉Promise对象：
//如果成功，执行这个函数：
var p2 = p1.then(function (result) {
    console.log('成功：' + result);
});
//如果失败，执行这个函数：
var p3 = p2.catch(function (reason) {
    console.log('失败：' + reason);
});
```

最大的好处是在异步执行的流程中，把执行代码和处理结果的代码清晰地分离了

![](2.png)

再看一个：

```javascript
'use strict';
function log(s) {
    var p = document.createElement('p');
    p.innerHTML = s;
    logging.appendChild(p);
}
// 0.5秒后返回input*input的计算结果:
function multiply(input) {
    return new Promise(function (resolve, reject) {
        log('calculating ' + input + ' x ' + input + '...');
        setTimeout(resolve, 500, input * input);
    });
}
// 0.5秒后返回input+input的计算结果:
function add(input) {
    return new Promise(function (resolve, reject) {
        log('calculating ' + input + ' + ' + input + '...');
        setTimeout(resolve, 500, input + input);
    });
}
var p = new Promise(function (resolve, reject) {
    log('start new Promise...');
    resolve(123);
});
p.then(multiply)
 .then(add)
 .then(multiply)
 .then(add)
 .then(function (result) {
    log('Got value: ' + result);
});
```

Promise还可以并行执行异步任务。