---
title: Js语法熟悉一
date: 2019-05-21 14:33:03
tags: Js
categories: 前端学习
---

# Js基本语法 

## 在哪里引入Js代码？

1）首先JavaScript代码可以直接嵌在网页的任何地方，不过通常我们都把JavaScript代码放到`<head>`中，我们看到网页的源码的时候被`<script>...</script>`包含的代码就是JavaScript代码，它会直接被浏览器执行。

2）单独的写一个 .js文件，然后在HTML中通过`<script src="..."></script>`引入这个文件。这样的话是方便多个网页应用到了同一份Js文件。

## 数据类型，运算符以及变量的申明

1）Js的数据类型主要有Number，布尔值，字符串。JavaScript不区分整数和浮点数，统一用Number表示。

```JavaScript
123; // 整数123
0.456; // 浮点数0.456
1.2345e3; // 科学计数法表示1.2345x1000，等同于1234.5
-99; // 负数
NaN; // NaN表示Not a Number，当无法计算结果时用NaN表示
Infinity; // Infinity表示无限大，当数值超过了JavaScript的Number所能表示的最大值时，就表示为Infinity
```

Number可以直接做四则运算，规则和数学一致。

注意一个点：字符串也可以使用加号，但是字符串将被级联但是注意字符串和数字相加会返回一个字符串，例如：

```javascript
var x = "8" + 3 + 5;
//输出结果：835
var x = 3 + 5 + "8";
//输出结果88
```

布尔值的话只能有false，true两种。

字符串的话就跟Java，c++的都差不多，不过JavaScript中以单引号'或双引号"括起来的任意文本都是字符串。

2）运算符：`&&`运算，`||`运算，`!`运算，比较运算符。基本上都差不多，但是要注意的是相等运算符`==`。JavaScript在设计时，有两种比较运算符：

第一种是`==`比较，它会自动转换数据类型再比较，很多时候，会得到非常诡异的结果；

第二种是`===`比较，它不会自动转换数据类型，如果数据类型不一致，返回`false`，如果一致，再比较。

通常的话我们是坚持使用`===`。

另外是`NaN`这个特殊的Number，它和其他所有的值都不相等，包括本身。唯一能判断`NaN`的方法是通过`isNaN()`函数。

3）变量的申明方式

变量名的要求：变量名是大小写英文、数字、`$`和`_`的组合，且不能用数字开头。变量名也不能是JavaScript的关键字，如`if`、`while`等。

```javascript
var age = 15;
var arr = [1, 2, 3.14, 'Hello', null, true];//JavaScript的数组可以包括任意数据类型
var person = {
    name: 'Bob',
    age: 20,
    tags: ['js', 'web', 'mobile'],
    city: 'Beijing',
    hasCar: true,
    zipcode: null
};//申明JavaScript的对象，它是由键-值组成的无序集合，引用的话和Java差不多，用对象变量.属性名的方式
```

同一个变量可以反复赋值，而且可以是不同类型的变量，但是要注意只能用`var`申明一次，注意在JavaScript中通过赋值可以修改变量的数据类型，JavaScript是一个动态的语言。像Java这种静态的语言就不能这样的进行赋值。

注意：不用`var`申明的变量会被视为全局变量，为了避免这一缺陷，所有的JavaScript代码都应该使用strict模式。我们在后面编写的JavaScript代码将全部采用strict模式。

## 字符串

在前面我们看到字符串就是用`''`或`""`括起来的字符。跟c++一样有转义字符，具体的熟悉一下就行。

通过`+`来连接字符串。

通过`s.length`来获取字符的长度

通过下标索引获取字符串某个指定位置的字符，索引号从0开始，需要注意的是，字符串是不可变的，如果对字符串的某个索引赋值，不会有任何错误，但是，也没有任何效果，如下：

```JavaScript
var s = 'Test';
s[0] = 'X';
alert(s); // s仍然为'Test'
```

`toUpperCase()`把一个字符串全部变为大写

`toLowerCase()`把一个字符串全部变为小写：

`indexOf()`会搜索指定字符串出现的位置（字符串第一个出现该字符的位置）

`substring()`返回指定索引区间的子串

`split()`把字符串转换为数组

```javascript
var txt = "a,b,c,d,e";   // 字符串
txt.split(",");          // 用逗号分隔
txt.split(" ");          // 用空格分隔
txt.split("|");          // 用竖线分隔
```

## 数组和对象

通过`arr.length`获取数组长度，直接给`Array`的`length`赋一个新的值会导致`Array`大小的变化如：

```JavaScript
var arr = [1, 2, 3];
arr.length; // 3
arr.length = 6;
arr; // arr变为[1, 2, 3, undefined, undefined, undefined]
arr.length = 2;
arr; // arr变为[1, 2]
```

和字符串不同，数组可以通过下表索引修改对应的元素值。但是要是索引超过范围的话同样会导致数组大小的变化。

通过`indexOf()`来搜索一个指定的元素的位置（同字符串第一个出现该字符的位置）

`slice()`截取`Array`的部分元素，然后返回一个新的`Array`

`push()`向`Array`的末尾添加若干元素，`pop()`则把`Array`的最后一个元素删除掉

往`Array`的头部添加若干元素，使用`unshift()`方法，`shift()`方法则把`Array`的第一个元素删掉

`sort()`可以对当前`Array`进行排序，它会直接修改当前`Array`的元素位置

`reverse()`把整个`Array`反转

`splice()`方法可以从指定的索引开始删除若干元素，然后再从该位置添加若干元素

`concat()`方法把当前的`Array`和另一个`Array`连接起来，并返回一个新的`Array`，并不是修改当前的数组。

`join()`方法能把当前`Array`的每个元素都用指定的字符串连接起来，然后返回连接后的字符串。

练习：在新生欢迎会上，你已经拿到了新同学的名单，请排序后显示：`欢迎XXX，XXX，XXX和XXX同学！`：

```JavaScript
'use strict';
var arr = ['小明', '小红', '大军', '阿黄'];
arr.sort();
console.log(`欢迎${arr[0]},${arr[1]},${arr[2]}和${arr[3]}同学!`);
```

JavaScript用一个`{...}`表示一个对象，键值对以`xxx: xxx`形式申明，用`,`隔开。注意，最后一个键值对不需要在末尾加`,`可以通过delete删除属性，赋值增加属性如：

```javascript
var xiaoming = {
    name: '小明',
    score: null
};
xiaoming.age = 18; // 新增一个age属性
xiaoming.age; // 18
delete xiaoming.age; // 删除age属性
xiaoming.age; // undefined
delete xiaoming['name']; // 删除name属性
xiaoming.name; // undefined
delete xiaoming.school; // 删除一个不存在的school属性也不会报错
```

利用`in`操作符检测是否拥有一个属性，但是注意所有对象最终都会在原型链上指向`object`，所以我们定义的对象都有`object`对象的属性，所以要判断一个属性是否是我们定义的对象拥有的，而不是继承得到的，可以用`hasOwnProperty()`方法

## 容器和一些控制语句

条件判断`if...else`，这些都差不多，就是逻辑的判断。

练习：小明身高1.75，体重80.5kg。请根据BMI公式（体重除以身高的平方）帮小明计算他的BMI指数，并根据BMI指数：

- 低于18.5：过轻
- 18.5-25：正常
- 25-28：过重
- 28-32：肥胖
- 高于32：严重肥胖

用`if...else...`判断并显示结果：

```javascript
'use strict';

var height = parseFloat(prompt('请输入身高(m):'));
var weight = parseFloat(prompt('请输入体重(kg):'));
var bmi=weight/height/height;
if (bmi < 18.5) {
    console.log('过轻');
} else if (bmi <= 25) {
    console.log('正常');
} else if (bmi < 28) {
    console.log('过重');
} else if (bmi < 32) {
    console.log('肥胖');
} else {
    console.log('严重肥胖');
}
```

循环也是一样利用`for`或者`while`或者`do...while`

练习:

利用`for`循环计算`1 * 2 * 3 * ... * 10`的结果：

```javascript
'use strict';
var x = 1;
var i;
for (i=1;i<=10;i++){
	x*=i;
}
if (x === 3628800) {
    console.log('1 x 2 x 3 x ... x 10 = ' + x);
}
else {
    console.log('计算错误');
}
```

请利用循环遍历数组中的每个名字，并显示`Hello, xxx!`：

```javascript
'use strict';
var arr = ['Bart', 'Lisa', 'Adam'];
arr.sort();  // 正序
//倒序arr.reverse();
for(var i=0;i<arr.length;i++){
	console.log(`Hello,${arr[i]}`);
}

var i=0;
while(i<arr.length){
	console.log(`Hello,${arr[i]}`);
	i++;
}
```

Map和Set容器

`Map`是一组键值对的结构，利用`key`和`value`。常用的函数有`set`，`delete`，`get`。用法都差不多。

`set`一组key的集合且不重复，常用的函数有`delete`，`add`。

`iterable`类型，`Array`、`Map`和`Set`都属于`iterable`类型。具有`iterable`类型的集合可以通过新的`for ... of`循环来遍历。因为遍历`Array`可以采用下标循环，遍历`Map`和`Set`就无法使用下标。如：

```javascript
var a = ['A', 'B', 'C'];
var s = new Set(['A', 'B', 'C']);
var m = new Map([[1, 'x'], [2, 'y'], [3, 'z']]);
for (var x of a) { // 遍历Array
    console.log(x);
}
for (var x of s) { // 遍历Set
    console.log(x);
}
for (var x of m) { // 遍历Map
    console.log(x[0] + '=' + x[1]);
}
```

或者通过`forEach()`方法循环，`forEach()`有三个参数

```javascript
var s = new Set(['A', 'B', 'C']);
s.forEach(function (element, sameElement, set) {
    console.log(element);
});
```

如果对某些参数不感兴趣，由于JavaScript的函数调用不要求参数必须一致，可以忽略。

# Js 函数

## 函数

函数定义如下：

```javascript
function abs(x) {
    if (x >= 0) {
        return x;
    } else {
        return -x;
    }
}
//或者另一种
var abs = function (x) {
    if (x >= 0) {
        return x;
    } else {
        return -x;
    }
};//注意这比上面多一个等号
```

JavaScript在调用参数的时候允许任意个参数调用，不管传多还是传少都不会报错。因此JavaScript提供了一个关键字`arguments`，它只在函数内部起作用，并且永远指向当前函数的调用者传入的所有参数。`arguments`类似`Array`但它不是一个`Array`，利用它可以判断传入参数的个数。

之后JavaScript又引入了rest参数，rest参数只能写在最后，前面用`...`标识，从运行结果可知，传入的参数先绑定`a`、`b`，多余的参数以数组形式交给变量`rest`，所以，不再需要`arguments`我们就获取了全部参数。如

```javascript
function foo(a, b, ...rest) {
    console.log('a = ' + a);
    console.log('b = ' + b);
    console.log(rest);
}

foo(1, 2, 3, 4, 5);
// 结果:
// a = 1
// b = 2
// Array [ 3, 4, 5 ]

foo(1);
// 结果:
// a = 1
// b = undefined
// Array []
```

练习：

请用rest参数编写一个`sum()`函数，接收任意个参数并返回它们的和：

```javascript
'use strict';
function sum(...rest) {
    var arr = [];
    arr = rest;   //获取所有的参数，以数组显示保存在arr数组中
    var result = 0;
    for(var i = 0; i < arr.length; i++){
        result = result + arr[i];
    }
    return result;
}
   // 测试:
var i, args = [];
for (i=1; i<=100; i++) {
    args.push(i);
}
if (sum() !== 0) {
    console.log('测试失败: sum() = ' + sum());
} else if (sum(1) !== 1) {
    console.log('测试失败: sum(1) = ' + sum(1));
} else if (sum(2, 3) !== 5) {
    console.log('测试失败: sum(2, 3) = ' + sum(2, 3));
} else if (sum.apply(null, args) !== 5050) {
    console.log('测试失败: sum(1, 2, 3, ..., 100) = ' + sum.apply(null, args));
} else {
    console.log('测试通过!');
}
```

定义一个计算圆面积的函数`area_of_circle()`，它有两个参数：

- r: 表示圆的半径；

- pi: 表示π的值，如果不传，则默认3.14

  ```javascript
  'use strict';
  
  function area_of_circle(r, pi) {
       var area;    
       if (arguments.length == 2) {    
           area = pi * r * r;    
       } else if (arguments.length < 2){    
           pi = 3.14;    
           area = pi * r * r;
       }
       else{
           console.log("arguments number must be 1 or 2.");
           return ;
       }
       return r * r * (pi || 3.14);
  }
  // 测试:
  if (area_of_circle(2) === 12.56 && area_of_circle(2, 3.1416) === 12.5664) {
      console.log('测试通过');
  } else {
      console.log('测试失败');
  }
  ```

## 函数变量作用域以及解析赋值

在JavaScript中，用`var`申明的变量实际上是有作用域的。一个变量在函数体内部申明，则该变量的作用域为整个函数体，在函数体外不可引用该变量，但是由于JavaScript的函数可以嵌套，此时，内部函数可以访问外部函数定义的变量。但是如果内部函数和外部函数的变量名重名的情况下在查找变量时从自身函数定义开始，从“内”向“外”查找。如果内部函数定义了与外部函数重名的变量，则内部函数的变量将“屏蔽”外部函数的变量。看样例：

```javascript
function foo() {
    var x = 1;
    function bar() {
        var x = 'A';
        console.log('x in bar() = ' + x); // 'A'
    }
    console.log('x in foo() = ' + x); // 1
    bar();
}
foo();
/*结果为
x in foo() = 1
x in bar() = A
*/
```

而且JavaScript的函数定义有个特点，它会先扫描整个函数体的语句，把所有申明的变量“提升”到函数顶部，也就是所有的变量都会在函数初步就定义了。

同时JavaScript默认有一个全局对象`window`，不在任何函数内定义的变量就具有全局作用域，全局作用域的变量实际上被绑定到`window`的一个属性。我们在之前说过函数定义有两种方式，以变量方式`var foo = function () {}`定义的函数实际上也是一个全局变量，因此，顶层函数的定义也被视为一个全局变量，并绑定到`window`对象，其实我们每次直接调用的`alert()`函数其实也是`window`的一个变量。

所以JavaScript中任何变量（函数也视为变量），如果没有在当前函数作用域中找到，就会继续往上查找，最后如果在全局作用域中也没有找到，则报`ReferenceError`错误。

因此在这个基础上，个人感觉引用了c++的`using namespace std`命名空间的概念，在JavaScript中有一个名字空间，因为全局变量会绑定到`window`上，不同的JavaScript文件如果使用了相同的全局变量，或者定义了相同名字的顶层函数，就会造成命名冲突，解决的方式就是把自己的所有变量和函数绑定到一个全局变量里面会减少全局变量冲突的几率。

```javascript
// 唯一的全局变量MYAPP:
var MYAPP = {};

// 其他变量:
MYAPP.name = 'myapp';
MYAPP.version = 1.0;

// 其他函数:
MYAPP.foo = function () {
    return 'foo';
};
```

解构赋值：在ES6后引入了这个解构赋值，直接对于多个变量同时赋值如：

```javascript
var [x, y, z] = ['hello', 'JavaScript', 'ES6'];
// x, y, z分别被赋值为数组对应元素:
console.log('x = ' + x + ', y = ' + y + ', z = ' + z);
/*结果为
x = hello, y = JavaScript, z = ES6
*/
根据数组的格式进行嵌套赋值，但是要注意嵌套层次和位置要保持一致
```

同理解构赋值也能对对象进行同样的操作如：

```javascript
var person = {
    name: '小明',
    age: 20,
    gender: 'male',
    passport: 'G-12345678',
    school: 'No.4 middle school',
    address: {
        city: 'Beijing',
        street: 'No.1 Road',
        zipcode: '100001'
    }
};
var {name, address: {city, zip}} = person;
name; // '小明'
city; // 'Beijing'
zip; // undefined, 因为属性名是zipcode而不是zip
// 注意: address不是变量，而是为了让city和zip获得嵌套的address对象的属性:
address; // Uncaught ReferenceError: address is not defined
//当然可以赋默认值避免了不存在的属性返回undefined的问题
```

## 方法和**高阶函数**

在一个对象中绑定函数，那么就成为这个对象的方法，如

```javascript
var xiaoming = {
    name: '小明',
    birth: 1990,
    age: function () {
        var y = new Date().getFullYear();
        return y - this.birth;
    }
};

xiaoming.age; // function xiaoming.age()
xiaoming.age(); // 今年调用是25,明年调用就变成26了
```

在函数内部`this`是一个特殊变量，它始终指向当前对象，也就是`xiaoming`这个变量。所以，`this.birth`可以拿到`xiaoming`的`birth`属性，但是我们要注意`this`这个指向，最好是在引用函数之前就把这个`this`给记录下来如`that`，然后在方法内定义其他的函数，用`that`去代替`this`；或者呢用`aplpy`函数去修复，`apply`它接收两个参数，第一个参数就是需要绑定的`this`变量，第二个参数是`Array`，表示函数本身的参数。同样有个`call`函数。

一个很重要的点**高阶函数**：

什么叫高阶函数？高阶函数指最少满足下列条件之一的函数：函数可以作为参数传递，函数可以作为返回值输出。（理解的不够，再细致的理解再补）

`map`/`reduce`函数：它们都是`Array`中定义的，我们调用`Array`的`map()`方法，传入我们自己的函数，就得到了一个新的`Array`作为结果，比方说我要得到一个数组中每个数的平方：

```javascript
'use strict';
function pow(x) {
    return x * x;
}
var arr = [1, 2, 3, 4, 5, 6, 7, 8, 9];
var results = arr.map(pow); //map()传入的参数是pow(),是函数本身
console.log(results);
// [1, 4, 9, 16, 25, 36, 49, 64, 81]
```

`reduce`函数的话是把一个函数作用在这个`Array`的`[x1, x2, x3...]`上，这个函数必须接收两个参数，`reduce()`把结果继续和序列的下一个元素做累积计算，实际能做的事情是对一个数组进行重复的处理，比方说求和，求积，如：

```javascript
’use strict';
function product(arr) {
	var ans=arr.reduce(function(x,y){
		return x*y;
	});
	return ans;
}
// 测试:
if (product([1, 2, 3, 4]) === 24 && product([0, 1, 2]) === 0 && product([99, 88, 77, 66]) === 44274384) {
    console.log('测试通过!');
}
else {
    console.log('测试失败!');
}
```

把`[1, 3, 5, 7, 9]`变换成整数13579，`reduce()`也能派上用场：

```javascript
var arr = [1, 3, 5, 7, 9];
arr.reduce(function (x, y) {
    return x * 10 + y;
}); // 13579
```

练习：

不要使用JavaScript内置的`parseInt()`函数，利用map和reduce操作实现一个`string2int()`函数：

```javascript
'use strict';
function string2int(s) {
    var arr= [];
    for(let i=0;i<s.length;i++)
        arr[i]=s[i]-'0';
    var ans=arr.reduce(function (x, y) {
    	return x * 10 + y;
	}); 
    return ans;
}
// 测试:
if (string2int('0') === 0 && string2int('12345') === 12345 && string2int('12300') === 12300) {
    if (string2int.toString().indexOf('parseInt') !== -1) {
        console.log('请勿使用parseInt()!');
    } else if (string2int.toString().indexOf('Number') !== -1) {
        console.log('请勿使用Number()!');
    } else {
        console.log('测试通过!');
    }
}
else {
    console.log('测试失败!');
}
```

请把用户输入的不规范的英文名字，变为首字母大写，其他小写的规范名字。输入：`['adam', 'LISA', 'barT']`，输出：`['Adam', 'Lisa', 'Bart']`。

```javascript
'use strict';
function normalize(arr) {
    return arr.map(function(x) {
        return x[0].toUpperCase() + x.substring(1).toLowerCase()
    });
}

// 测试:
if (normalize(['adam', 'LISA', 'barT']).toString() === ['Adam', 'Lisa', 'Bart'].toString()) {
    console.log('测试通过!');
}
else {
    console.log('测试失败!');
}
```

小明希望利用`map()`把字符串变成整数，他写的代码很简洁：

```javascript
'use strict';
var arr = ['1', '2', '3'];
var r;
r = arr.map(parseInt);
console.log(r);
结果竟然是1, NaN, NaN，小明百思不得其解，请帮他找到原因并修正代码。
```

这个主要是parseInt的一些误区，后序会写篇来介绍这个，先暂时性看这个理解一下<https://blog.csdn.net/wjl84945979/article/details/56478927>

`filter`函数`filter()`把传入的函数依次作用于每个元素，然后根据返回值是`true`还是`false`决定保留还是丢弃该元素。类似一个筛选的功能，例如：在一个`Array`中，删掉偶数，只保留奇数

```javascript
var arr = [1, 2, 4, 5, 6, 9, 10, 15];
var r = arr.filter(function (element, index, self) {
    return element% 2 !== 0;
});
r; // [1, 5, 9, 15]
```

看上面的调用`filter()`接收的回调函数，其实可以有多个参数。通常我们仅使用第一个参数，表示`Array`的某个元素。回调函数还可以接收另外两个参数，表示元素的位置和数组本身。

练习：

请尝试用`filter()`筛选出素数：

```javascript
'use strict';
function get_primes(arr) {
    var ans=arr.filter(function (element,index,self) {
        if(element<2 )
            return false;
        else{
            var flag=false;
            for(let i=2;i<element;i++){
                if(element%i===0){
                    flag=true;
                    break;
                }
            }
            if(flag)
                return false;
            else
                return true;
        }
    });
    return ans;
}
// 测试:
var
    x,
    r,
    arr = [];
for (x = 1; x < 100; x++) {
    arr.push(x);
}
r = get_primes(arr);
if (r.toString() === [2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67, 71, 73, 79, 83, 89, 97].toString()) {
    console.log('测试通过!');
} else {
    console.log('测试失败: ' + r.toString());
}
```









