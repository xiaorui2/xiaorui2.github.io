---
title: ES6熟悉
date: 2019-06-08 17:47:48
tags: ES6
categories: 前端学习
---

# Let和Const命令

## Let

ES6 新增了`let`命令，用来声明变量。它的用法类似于`var`，但是所声明的变量，只在`let`命令所在的代码块内有效。

```react
{
  let a = 10;
  var b = 1;
}
a // ReferenceError: a is not defined.
b // 1
```

用`let`声明的变量不会进行变量提升，我们知道用`var`声明的变量定义的话是会进行变量提升，即使你在变量定义之前使用也只是用返回一个`undefined`，但是你要是用`let`声明的话是会抛出一个错误，因为如果区块中存在`let`和`const`命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错。

```react
// var 的情况
console.log(foo); // 输出undefined
var foo = 2;
// let 的情况
console.log(bar); // 报错ReferenceError
let bar = 2;
```

`for`循环的时候就很适合使用`let`，但是注意一个点，就是设置循环变量的那部分是一个父作用域，而循环体内部是一个单独的子作用域。如：

```react
for (let i = 0; i < 3; i++) {
  let i = 'abc';
  console.log(i);
}
// abc
// abc
// abc
```

上面代码正确运行，输出了 3 次`abc`。这表明函数内部的变量`i`与循环变量`i`不在同一个作用域，有各自单独的作用域。

`let`不允许在相同作用域内，重复声明同一个变量，但是允许块级作用域的任意嵌套，然后内层作用域可以定义外层作用域的同名变量。

为什么需要块级作用域？

第一种场景，内层变量可能会覆盖外层变量。

第二种场景，用来计数的循环变量泄露为全局变量。

块级作用域之中，函数声明语句的行为类似于`let`，在块级作用域之外不可引用。但是注意函数声明类似于`var`，即会提升到全局作用域或函数作用域的头部，同时，函数声明还会提升到所在的块级作用域的头部。比方这个实例：

```react
// 浏览器的 ES6 环境
function f() { console.log('I am outside!'); }
(function () {
  if (false) {
    // 重复声明一次函数f
    function f() { console.log('I am inside!'); }
  }
  f();
}());
/* Uncaught TypeError: f is not a function，它会报错，说f并不是函数类型，这是因为上述说的问题，这样声明的情况下，代码等价于下面*/
function f() { console.log('I am outside!'); }
(function () {
  var f = undefined;
  if (false) {
    function f() { console.log('I am inside!'); }
  }
  f();
}());
//因此f在定义的时候不是一个函数后序又没有进行函数的声明调用导致报错
```

所以在块级作用域汇总避免声明函数，如果非要声明的话应该写成函数表达式的形式。另外，还有一个需要注意的地方。ES6 的块级作用域必须有大括号，而`let`只能出现在当前作用域的顶层。

## Const

`const`声明一个只读的常量。一旦声明，常量的值就不能改变，并且必须立即初始化。`const`的作用域与`let`命令相同：只在声明所在的块级作用域内有效。

`const`实际上保证的，并不是变量的值不得改动，而是变量指向的那个内存地址所保存的数据不得改动。对于简单类型的数据（数值、字符串、布尔值），值就保存在变量指向的那个内存地址，因此等同于常量。但对于复合类型的数据（主要是对象和数组），变量指向的内存地址，保存的只是一个指向实际数据的指针，`const`只能保证这个指针是固定的，但是不能保证这对象或者数组里面的内容不会进行改变，不会变的知识这个地址，对象依旧能够添加新的对象。

如果真的想将对象冻结，应该使用`Object.freeze`方法。

```react
const foo = Object.freeze({});
// 常规模式时，下面一行不起作用；
// 严格模式时，该行会报错
foo.prop = 123;
```

注意：`ES6`有六种声明变量的方法：`let`，`const`，`var`，`function`，`class`，`import`。

# 变量的解构赋值

ES6 允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称为解，如下图：

```react
let [a, b, c] = [1, 2, 3];
```

上面代码表示，可以从数组中提取值，按照对应位置，对变量赋值。这种写法属于“模式匹配”，只要等号两边的模式相同，左边的变量就会被赋予对应的值，如果解构不成功，变量的值就等于`undefined`。

解构赋值允许指定默认值，如果默认值是一个表达式，那么这个表达式是惰性求值的，即只有在用到的时候，才会求值。

## 什么时候能用解构赋值？

只要某种数据结构具有 Iterator 接口，都可以采用数组形式的解构赋值。像set，array，map。对象也可以进行解构赋值。

## 对象解构赋值的注意点

对象的解构赋值的内部机制，是先找到同名属性，然后再赋给对应的变量。真正被赋值的是后者，而不是前者。如：

```react
let { foo: baz } = { foo: 'aaa', bar: 'bbb' };
baz // "aaa"
foo // error: foo is not defined
//foo是匹配的模式，baz才是变量。真正被赋值的是变量baz，而不是模式foo。
```

对象结构赋值的时候也可以进行嵌套赋值，但是如果解构模式是嵌套的对象，而且子对象所在的父属性不存在，那么将会报错。当然对象的解构赋值也可以指定默认值。

除了这些还有：

如果要将一个已经声明的变量用于解构赋值，必须非常小心。

```react
// 错误的写法
let x;
{x} = {x: 1};
// 正确的写法
let x;
({x} = {x: 1});
//为 JavaScript 引擎会将{x}理解成一个代码块，从而发生语法错误。
```

解构赋值允许等号左边的模式之中，不放置任何变量名。因此，可以写出非常古怪的赋值表达式。

由于数组本质是特殊的对象，因此可以对数组进行对象属性的解构。

## 解构赋值的圆括号问题

解构赋值虽然很方便，但是解析起来并不容易。对于编译器来说，一个式子到底是模式，还是表达式，没有办法从一开始就知道，必须解析到（或解析不到）等号才能知道。

由此带来的问题是，如果模式中出现圆括号怎么处理，ES6规定了几种不能使用圆括号的情况：

（1）变量声明语句（2）函数参数（3）赋值语句的模式

看下实例：

```react
// 变量声明错误
let [(a)] = [1];
let {x: (c)} = {};
let ({x: c}) = {};
let {(x: c)} = {};
let {(x): c} = {};
let { o: ({ p: p }) } = { o: { p: 2 } };
//函数参数
// 报错
function f([(z)]) { return z; }
// 报错
function f([z,(x)]) { return x; }
// 赋值语句的模式全部报错
({ p: a }) = { p: 42 };
([a]) = [5];
```

## 解构赋值的用处

1：交换变量的值

2：从函数返回多个值

3：函数参数的定义（可以方便地将一组参数与变量名对应起来）

4：提取 JSON 数据

5：函数参数的默认值

6：遍历 Map 结构

7：输入模块的指定方法

# Module

众所周知`JavaScript`是没有`module`的概念的，无法将一个大程序拆分成互相依赖的小文件，再用简单的方法拼装起来。在`ES6`之前，社区制定了一些模块加载方案，最主要的有 `CommonJS` 和 `AMD` 两种。前者用于服务器，后者用于浏览器，在`ES6`中，通过`export`命令显式指定输出的代码，再通过`import`命令输入，这种加载称为“编译时加载”或者静态加载，即 `ES6` 可以在编译时就完成模块加载。但是不是说有这个之后就不需要动态加载了，特别是在浏览器工作的时候有时候是不需要把所有依赖的东西都导入的，可以到你需要的时候导入。

## Export

一个模块就是一个独立的文件。该文件内部的所有变量，外部无法获取。如果你希望外部能够读取模块内部的某个变量，就必须使用`export`关键字输出该变量。`export`命令除了输出变量，还可以输出函数或类。通常情况下，`export`输出的变量就是本来的名字，但是可以使用`as`关键字重命名。

```react
//输出变量
export var year = 1958;
var year = 1958;
var firstName = 'Michael';
var lastName = 'Jackson';
export { firstName, lastName, year };
//输出函数或者类
export function multiply(x, y) {
  return x * y;
};
//重命名
function v1() { ... }
function v2() { ... }
export {
  v1 as streamV1,
  v2 as streamV2,
  v2 as streamLatestVersion
};
```

`export`命令可以出现在模块的任何位置，只要处于模块顶层就可以。如果处于块级作用域内，就会报错。

## Import

`export`命令定义了模块的对外接口以后，其他 `JS` 文件就可以通过`import`命令加载这个模块。

`import`命令具有提升效果，会提升到整个模块的头部，首先执行，因为它是编译时期执行的，所以不能使用表达式和变量并且不能放在判断语句这些里面，这些只有在运行时才能得到结果的语法结构。

如果多次重复执行同一句`import`语句，那么只会执行一次，而不会执行多次。

## Export Default

它的默认输出是一个函数。

```react
// export-default.js
export default function () {
  console.log('foo');
}
// import-default.js
import customName from './export-default';
customName(); // 'foo'
```

上面代码的`import`命令，可以用任意名称指向`export-default.js`输出的方法，这时就不需要知道原模块输出的函数名。需要注意的是，这时`import`命令后面，不使用大括号。

`export default`命令用于指定模块的默认输出。显然，一个模块只能有一个默认输出，因此`export default`命令只能使用一次。

如果在一个模块之中，先输入后输出同一个模块，`import`语句可以与`export`语句写在一起。但是注意引入的模块的内容只能字啊`import`和`export`内容之间被引用。

## Import()

前面介绍过，`import`命令会被 JavaScript 引擎静态分析，先于模块内的其他语句执行所以，下面的代码会报错。

```react
if (x === 2) {
  import MyModual from './myModual';
}
```

上面代码中，引擎处理`import`语句是在编译时，这时不会去分析或执行`if`语句，所以`import`语句放在`if`代码块之中毫无意义，因此会报句法错误，这样的设计，固然有利于编译器提高效率，但也导致无法在运行时加载模块。在语法上，条件加载就不可能实现。所以引入`import()`函数以帮助完成动态加载。

`import`函数的参数`specifier`，指定所要加载的模块的位置。`import`命令能够接受什么参数，`import()`函数就能接受什么参数，两者区别主要是后者为动态加载。

`import()`返回一个 Promise 对象。

```react
const main = document.querySelector('main');
import(`./section-modules/${someVariable}.js`)
  .then(module => {
    module.loadPageInto(main);
  })
  .catch(err => {
    main.textContent = err.message;
  });
```

它一般会用于：按需加载，条件加载，动态的模块路径。

## Module加载实现

### 浏览器加载

HTML 网页中，浏览器通过`<script>`标签加载 JavaScript 脚本。默认情况下，浏览器是同步加载 JavaScript 脚本，即渲染引擎遇到`<script>`标签就会停下来，等到执行完脚本，再继续向下渲染。如果是外部脚本，还必须加入脚本下载的时间。如果脚本体积很大，下载和执行的时间就会很长，因此造成浏览器堵塞，所以浏览器允许脚本异步加载，下面就是两种异步加载的语法。

```html
<script src="path/to/myModule.js" defer></script>
<script src="path/to/myModule.js" async></script>
```

`defer`与`async`的区别是：`defer`要等到整个页面在内存中正常渲染结束（DOM 结构完全生成，以及其他脚本执行完成）才会执行；`async`一旦下载完，渲染引擎就会中断渲染，执行这个脚本以后，再继续渲染。一句话，`defer`是“渲染完再执行”，`async`是“下载完就执行”。另外，如果有多个`defer`脚本，会按照它们在页面出现的顺序加载，而多个`async`脚本是不能保证加载顺序的。

浏览器加载 ES6 模块，也使用`<script>`标签，但是要加入`type="module"`属性。

```html
<script type="module" src="./foo.js"></script>
```

浏览器对于带有`type="module"`的`<script>`，都是异步加载，不会造成堵塞浏览器，即等到整个页面渲染完，再执行模块脚本，等同于打开了`<script>`标签的`defer`属性。

利用顶层的`this`等于`undefined`这个语法点，可以侦测当前代码是否在 ES6 模块之中。

# Class

## Class概念

ES6 的`class`可以看作只是一个语法糖，它的绝大部分功能，ES5 都可以做到，新的`class`写法只是让对象原型的写法更加清晰、更像面向对象编程的语法而已。利用class就很像我们之前在c++，java当中学习的对象。

```react
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }
  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }
}
/*constructor方法，这就是构造方法;this关键字则代表实例对象,Point类除了构造方法，还定义了一个toString方法。注意，定义“类”的方法的时候，前面不需要加上function这个关键字，直接把函数定义放进去了就可以了。另外，方法之间不需要逗号分隔，加了会报错。*/
```

注意：类的数据类型就是函数，类本身就指向构造函数。而且类的方法都定义在`prototype`对象上面。

`constructor`方法是类的默认方法，通过`new`命令生成对象实例时，自动调用该方法。一个类必须有`constructor`方法，如果没有显式定义，一个空的`constructor`方法会被默认添加。

类必须使用`new`调用，否则会报错。

类的所有实例共享一个原型对象。

```react
var p1 = new Point(2,3);
var p2 = new Point(3,2);
p1.__proto__ === p2.__proto__
/*true，p1和p2都是Point的实例，它们的原型都是Point.prototype，所以__proto__属性是相等的。*/
```

实例的属性除非显式定义在其本身（即定义在`this`对象上），否则都是定义在原型上（即定义在`class`上）。

```react
//定义类
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }
  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }

}
var point = new Point(2, 3);
point.toString() // (2, 3)
point.hasOwnProperty('x') // true
point.hasOwnProperty('y') // true
point.hasOwnProperty('toString') // false
point.__proto__.hasOwnProperty('toString') // true
/*上面代码中，x和y都是实例对象point自身的属性（因为定义在this变量上），所以hasOwnProperty方法返回true，而toString是原型对象的属性（因为定义在Point类上），所以hasOwnProperty方法返回false。*/
```

### 类的属性名，可以采用表达式。

```react
let methodName = 'getArea';
class Square {
  constructor(length) {
    // ...
  }
  [methodName]() {
    // ...
  }
}
//Square类的方法名getArea，是从表达式得到的。
```

### Class表达式

与函数一样，类也可以使用表达式的形式定义。

```react
const MyClass = class Me {
  getClassName() {
    return Me.name;
  }
};
```

上面代码使用表达式定义了一个类。需要注意的是，这个类的名字是`Me`，但是`Me`只在 Class 的内部可用，指代当前类。在 Class 外部，这个类只能用`MyClass`引用。

### 静态方法

类相当于实例的原型，所有在类中定义的方法，都会被实例继承。如果在一个方法前，加上`static`关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就称为“静态方法”。

```react
class Foo {
  static classMethod() {
    return 'hello';
  }
}
Foo.classMethod() // 'hello'
var foo = new Foo();
foo.classMethod()
// TypeError: foo.classMethod is not a function
```

如果静态方法包含`this`关键字，这个`this`指的是类，而不是实例

```react
class Foo {
  static bar() {
    this.baz();
  }
  static baz() {
    console.log('hello');
  }
  baz() {
    console.log('world');
  }
}
Foo.bar() // hello
/*静态方法bar调用了this.baz，这里的this指的是Foo类，而不是Foo的实例，等同于调用Foo.baz。另外，从这个例子还可以看出，静态方法可以与非静态方法重名。*/
```

父类的静态方法，可以被子类继承。静态方法也是可以从`super`对象上调用的。

## Class继承

Class 可以通过`extends`关键字实现继承

```
class ColorPoint extends Point {
  constructor(x, y, color) {
    super(x, y); // 调用父类的constructor(x, y)
    this.color = color;
  }
  toString() {
    return this.color + ' ' + super.toString(); // 调用父类的toString()
  }
}
```

子类必须在`constructor`方法中调用`super`方法，否则新建实例时会报错。这是因为子类自己的`this`对象，必须先通过父类的构造函数完成塑造，得到与父类同样的实例属性和方法，然后再对其进行加工，加上子类自己的实例属性和方法。如果不调用`super`方法，子类就得不到`this`对象。

`Object.getPrototypeOf`方法可以用来从子类上获取父类。可以使用这个方法判断，一个类是否继承了另一个类。

### super

`super`这个关键字，既可以当作函数使用，也可以当作对象使用。在这两种情况下，它的用法完全不同。

第一种情况，`super`作为函数调用时，代表父类的构造函数。就是我们上面写的代码中就能看出来。

第二种情况，`super`作为对象时，在普通方法中，指向父类的原型对象；在静态方法中，指向父类。

```react
class A {
  p() {
    return 2;
  }
}
class B extends A {
  constructor() {
    super();
    console.log(super.p()); // 2
  }
}
let b = new B();
/*子类B当中的super.p()，就是将super当作一个对象使用。这时，super在普通方法之中，指向A.prototype，所以super.p()就相当于A.prototype.p()。*/
```

由于`super`指向父类的原型对象，所以定义在父类实例上的方法或属性，是无法通过`super`调用的。

### prototype属性和__proto__属性

每一个对象都有`__proto__`属性，指向对应的构造函数的`prototype`属性。Class 作为构造函数的语法糖，同时有`prototype`属性和`__proto__`属性，因此同时存在两条继承链。

子类的`__proto__`属性，表示构造函数的继承，总是指向父类。

子类`prototype`属性的`__proto__`属性，表示方法的继承，总是指向父类的`prototype`属性。