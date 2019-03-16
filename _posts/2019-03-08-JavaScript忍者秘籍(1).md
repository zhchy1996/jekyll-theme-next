---
title: JavaScript忍者秘籍(1)
description: JavaScript忍者秘籍读书笔记
categories:
- JavaScript
tags:
- 笔记
- JavaScript
---

### 第一章
#### 版本支持
可以通过以下网址查看浏览器对各个版本js的支持情况:  
1. https://kangax.github.io/compat-table/es6/
2. http://kangax. github. io/compat-table/es2016plus/ 
3. https://kangax.github.io/compat-table/esnext/

#### 性能分析
可以通过`console.time`和`console.timeEnd`来展示一段代码执行的事件

#### 跨平台开发能力
* 桌面应用：
  * [NW.js](http://nwjs.io)
  * [Electron](http://electron.atom.io)
* 移动应用：
  * [apache Cordova](https://cordova.apache.org)
* Node.js

### 第二章：运行时的页面构建过程 
#### 输入url发生了什么
![](/img/JavaScript忍者秘籍/2-1.jpg)
- - - -
##### 页面构建阶段
![](/img/JavaScript忍者秘籍/2-2.jpg)
![](/img/JavaScript忍者秘籍/2-3.jpg)
> dom与html并不相同，html更像是dom的蓝图，浏览器根据html构建dom，如果html有错误浏览器会修复它发现的问题
> 在构建时如果遇到脚本元素浏览器会停止构建dom执行script代码

当所有的html元素和js代码都执行完毕后就进入生命周期第二部分：事件处理。
- - - -
##### 事件处理
![](/img/JavaScript忍者秘籍/2-4.jpg)

事件有两种注册方式
  * 通过把函数赋给某个特殊属性(如onload)
  * 通过使用内置addEventListener方法
- - - -
### 第三章：理解函数
#### 函数作为对象的乐趣
可以像给对象添加属性一样给函数添加属性
```js
var fn = function () {};
fn.a = 'a';
```
这样函数就拥有了自我记忆的能力
```js
function isPrime(value) {
　if (!isPrime.answers) {
　　isPrime.answers = {};
　}　　←---　创建缓存
　if (isPrime.answers[value] !== undefined) {
　　return isPrime.answers[value];
　}　　←---　检查缓存的值
　var prime = value !== 0 && value !== 1; // 1 is not a prime
　for (var i = 2; i < value; i++) {
　　 if (value % i === 0) {
　　　　prime = false;
　　　　break;
　　 }
　}
　return isPrime.answers[value] = prime; 　　←---　存储计算的值
}

assert(isPrime(5), "5 is prime!");
assert(isPrime.answers[5], "The answer was cached!"); 　　←---　测试该函数是否正常工作
```
> 这样做有两个优点：
> 1. 由于函数调用时会寻找之前调用所得到的值，所以用户最终会乐于看到所获得的性能收益。
> 2. 它几乎是无缝地发生在后台，最终用户和页面作者都不需要执行任何特殊请求，也不需要做任何额外初始化，就能顺利进行工作。

> 但是它也有以下缺点：
> 1. 任何类型的缓存都必然会为性能牺牲内存。
> 2. 纯粹主义者会认为缓存逻辑不应该和业务逻辑混合，函数或方法只需要把一件事做好。但不必担心，在第8章你会了解到如何解决这类问题。
> 3. 对于这类问题很难做负载测试或估计算法复杂度，因为结果依赖于函数之前的输入。

- - - -
#### 函数声明和函数表达式
函数声明
```js
function a() {}
```
函数表达式
```js
var a = function() {};
myFunction(function() {});
```
> 这种总是其他表达式一部分的函数(作为复制表达式的右值，或者作为其他函数的参数)叫做函数表达式。
> 对于函数声明来说，函数名是强制性的，而对于函数表达式来说，函数名则完全是可选的。
立即函数是一种函数表达式

```js
(function() {})();
+function(){}();
-function(){}();
!function(){}();
~function(){}();
// 少见的写法
(function() {}());
```
> 之所以用括号包裹是为了告诉js解释器这条语句是函数表达式，否则它会被当做函数声明

- - - -
#### 剩余参数
```js
function multiMax(first, ...other) {
  // 多余的参数会存在other数组中
  console.log(other)
}
```
> 需要注意的是只有最后一个形参可以是剩余参数

- - - -
#### 默认参数
```js
function (a = 'b') {
  console.log(a) // b
}
```
- - - -
### 第四章：函数进阶，理解函数调用
#### arguments
其为一个类数组，包含了所有传入函数的实参,其不可调用数组方法。

改变arguments中的值也会改变函数对应实参的值,**但是在严格模式中改变arguments中的值并不会改变函数实参**

- - - -
#### this
#### 函数调用
有四种方式调用一个函数
* fun()
* obj.fun()
* new fun()
* fun.apply(obj)

##### 直接调用
此时this有两种可能
1. 在非严格模式下，this为window
2. 在严格模式下，this为undefined

##### 作为方法被调用
此时的this为该方法的宿主对象

##### 构造函数
使用new关键字会触发以下几个动作：
1. 创建一个新的空对象
2. 将该对象作为this传给构造函数，作为函数上下文
3. 新构造的对象作为new运算符的返回值

如果构造函数返回一个对象则将这个对象作为整个表达式的值返回，this被抛弃  
如果构造函数返回的是非对象类型，则忽略返回值，返回新创建的对象

##### 使用apply和call方法调用
当为一个dom元素绑定方法时this会指向dom本身

apply和call可以指定上下文

- - - -
#### 解决函数上下文的问题

##### 使用箭头函数
箭头函数的this与生命所在的上下文this相同，箭头函数执行时不会传入this而是继承所在上下文的this

##### 使用bind方法
所有函数均可访问bind方法，可以创建并返回一个新函数，并绑定在传入的对象上。

- - - -
### 第五章：精通函数，闭包和作用域

#### 理解闭包
闭包允许函数访问并操作函数外部的变量。只要变量或函数存在于生命函数时的作用域内，闭包即可使函数能够访问这些变量或函数。
```js
var outerValue = 'ninja';
function outerFunction() {
  outerValue === 'ninja';
}
outerFunction();
```
> 外部变量outerValue和外部函数outerFunction都是在全局作用域中声明的，该作用域（实际上就是一个闭包）从未消失（只要应用处于运行状态）。这也不足为奇，该函数可以访问到外部变量，因为它仍然在作用域内并且是可见的。
