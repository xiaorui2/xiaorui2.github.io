---
title: Js语法熟悉二
date: 2019-05-22 18:25:53
tags: Js
categories: 前端学习
---

# JavaScript输出

## 使用`window.alert()`

```
<!DOCTYPE html>
<html>
<body>
<script>
window.alert(5 + 6);
</script>
</body>
</html> 
```

## 使用`innerHTML`写入Html元素

如需访问 HTML 元素，JavaScript 可使用 `document.getElementById(id)` 方法。

```
<!DOCTYPE html>
<html>
<body>
<p>我的第一个段落</p>
<p id="demo"></p>
<script>
 document.getElementById("demo").innerHTML = 5 + 6;
</script>
</body>
</html> 
```

## `console.log()`写入控制台

```
<!DOCTYPE html>
<html>
<body>
<script>
console.log(5 + 6);
</script>
</body>
</html>
```

## `使用document.write()`写入Html元素

```
<!DOCTYPE html>
<html>
<body>
<script>
document.write(5 + 6);
</script>
</body>
</html> 
```

但是在 HTML 文档完全加载后使用 `document.write()`将删除所有已有的 HTML，所以只用于测试

# 什么时候产生NaN

## 表达式计算

一个表达式中如果有减号 (-)、乘号 (*) 或 除号 (/) 等运算符时，JS 引擎在计算之前，会试图将表达式的每个分项转化为 Number 类型（使用 Number(x) 做转换）。如果转换失败，表达式将返回 NaN 。

```javascript
100 - '2a' ; // NaN
'100' / '20a'; // NaN
'20a' * 5 ; // NaN
undefined - 1; // NaN, Number(undefined) == NaN
[] * 20 ; // 0, Number([]) == 0
null - 5; // -5, Number(null) == 0
```

## 类型转换

直接使用 parseInt，parseFloat 或 Number 将一个非数字的值转化为数字时，表达式返回 NaN ,但是对于 数字+字符的值，其转化结果会有所不同，Number 转换的是整个值，而不是部分值；parseInt 和 parseFloat 只转化第一个无效字符之前的字符串。 另外，一元加操作符也可以实现与 Number 相同的作用。　：

```javascript
'abc' - 3   // NaN
parseInt('abc')  // NaN
parseFloat('abc') // NaN
Number('abc')    // NaN
Number('123abc'); // NaN
parseInt('123abc'); // 123
parseInt('123abc45'); // 123
parseFloat('123.45abc');// 123.45
+ '12abc'; // NaN
+ '123'; // 123
+ '123.78'; // 123.78
+ 'abc'; // NaN
```

# 三个标准对象

## Date

`Date`对象用来表示日期和时间。`Date`常用的一些函数：

```javascript
var now = new Date();//获取系统当前时间
now; 
now.getFullYear(); // 2019, 获得now这个Date的年份
now.getMonth(); // 4, 获得now这个Date的月份，注意月份范围是0~11，4表示五月
now.getDate(); // 22, 获得now这个Date的日期，表示24号
now.getDay(); // 3, 获得now这个Date的星期几，表示星期三
now.getHours(); // 18, 获得now这个Date的小时，24小时制
now.getMinutes(); // 30, 获得now这个Date的分钟
now.getSeconds(); // 22, 获得now这个Date的秒数
now.getMilliseconds(); // 426, 获得now这个Date的毫秒数
now.getTime(); // 1558521017632, 以number形式表示的时间戳，它表示从1970年1月1日零时整的GMT时区开始的那一刻，到现在的毫秒数，时间戳可以精确地表示一个时刻，并且与时区无关
var d = new Date(2015, 5, 19, 20, 15, 30, 123);//创建一个自己要求的Date
d; // Fri Jun 19 2015 20:15:30 GMT+0800 (CST)
var d = Date.parse('2015-06-24T19:49:22.875+08:00');//创建一个自己要求的Date的第二种方法
d; // 1435146562875
```

注意 `JavaScript`的`Date`对象月份值从0开始，牢记0=1月，1=2月，2=3月，……，11=12月。

## Json

首先Json是一种数据传输的格式。

其次把任何JavaScript对象变成JSON，就是把这个对象序列化成一个JSON格式的字符串。对应的如果我们收到一个JSON格式的字符串，只需要把它反序列化成一个JavaScript对象，就可以在JavaScript中直接使用这个对象了。

序列化是通过`JSON.stringify(JavaScirpt的对象)`来实现的。这个函数有三个参数，第一个就是我们要序列化的JavaScript的对象，第二个参数是用来控制如何筛选对象的键值，比方可以传函数，传要取出来的属性，第三个是控制的转换后的格式。如果我们还想要精确控制如何序列化一个对象的话如`xiaoming`，可以给`xiaoming`定义一个`toJSON()`的方法，直接返回JSON应该序列化的数据：

```javascript
var xiaoming = {
    name: '小明',
    age: 14,
    gender: true,
    height: 1.65,
    grade: null,
    'middle-school': '\"W3C\" Middle School',
    skills: ['JavaScript', 'Java', 'Python', 'Lisp'],
    toJSON: function () {
        return { // 只输出name和age，并且改变了key：
            'Name': this.name,
            'Age': this.age
        };
    }
};
JSON.stringify(xiaoming); // '{"Name":"小明","Age":14}'
```

反序列化是通过用`JSON.parse()`把它变成一个JavaScript对象，同时这个函数还可以接收一个函数作为参数，用来转换解析出的属性：

```javascript
var obj = JSON.parse('{"name":"小明","age":14}', function (key, value) {
    if (key === 'name') {
        return value + '同学';
    }
    return value;
});
console.log(JSON.stringify(obj)); // {name: '小明同学', age: 14}
```

## RegExp(待补)





# 面向对象

## 创建对象

JavaScript对每个创建的对象都会设置一个原型，指向它的原型对象。形成一个原型链，当我们要访问一个对象的一个属性的时候，就会通过这个原型链查找，如果一直找到`Object.prototype`对象，都还没有找到，就只能返回`undefined`。

比方说我们创建了一个Array对象，我们可以得到其对应的原型链如下：

```javascript
var arr = [1, 2, 3];
arr -> Array.prototype -> Object.prototype -> null
```

我们这个`arr`对象，能调用`arr，Array.prototype，Object.prototype`上定义的所有的方法。

构造函数，除了直接用`{...}`创建一个函数之外，还可以通过构造函数创建对象

```javascript
function Student(name) {
    this.name = name;
    this.hello = function () {
        alert('Hello, ' + this.name + '!');
    }
}
var xiaoming = new Student('小明');
xiaoming.name; // '小明'
xiaoming.hello(); // Hello, 小明!
```

这是一个普通函数，但是在JavaScript中，可以用关键字`new`来调用这个函数，并返回一个对象,注意，如果不写`new`，这就是一个普通函数，它返回`undefined`。

## class继承

在`ES6`开始引入了`class`这个概念，就很类似`Java`的`class`对象了，例如：

```javascript
class Student {
    constructor(name) {//构造函数
        this.name = name;
    }
    hello() {//普通函数
        alert('Hello, ' + this.name + '!');
    }
}
```

继承的话和`Java`也很像，利用`extends`关键字

```javascript
class PrimaryStudent extends Student {
    constructor(name, grade) {
        super(name); // 记得用super调用父类的构造方法!
        this.grade = grade;
    }
    myGrade() {
        alert('I am at grade ' + this.grade);
    }
}
```

# 错误处理

## 错误捕获

这个和`Java`也很像，利用`try ... catch`捕获。

### 如果代码发生了错误，又没有被`try ... catch`捕获，那么，程序执行流程会跳转到哪呢？

如果在一个函数内部发生了错误，它自身没有捕获，错误就会被抛到外层调用函数，如果外层函数也没有捕获，该错误会一直沿着函数调用链向上抛出，直到被JavaScript引擎捕获，代码终止执行。

### 利用`try ... catch`捕获错误

```javascript
function main(s) {
    console.log('BEGIN main()');
    try {
        foo(s);
    } catch (e) {
        console.log('出错了：' + e);
    }
    console.log('END main()');
}
function foo(s) {
    console.log('BEGIN foo()');
    bar(s);
    console.log('END foo()');
}
function bar(s) {
    console.log('BEGIN bar()');
    console.log('length = ' + s.length);
    console.log('END bar()');
}
main(null);
```

