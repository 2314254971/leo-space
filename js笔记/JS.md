# JS

## 数据类型

**值类型(基本类型)**：字符串（String）、数字(Number)、布尔(Boolean)、空（Null）、未定义（Undefined）、Symbol。

**引用数据类型（对象类型）**：对象(Object)、数组(Array)、函数(Function)，还有两个特殊的对象：正则（RegExp）和日期（Date）

![](C:\Users\23142\Desktop\js笔记\Javascript-DataType.png)

或者按照另外方式区分：

```javascript
typeof "John"                 
// 返回 string 
typeof 3.14                   
// 返回 number
typeof NaN                    
// 返回 numbertypeof false                  // 返回 booleantypeof [1,2,3,4]              // 返回 object
typeof {name:'John', age:34} 
// 返回 objecttypeof new Date()             
// 返回 objecttypeof function () {}         // 返回 function
typeof myCar                  
// 返回 undefined (如果 myCar 没有声明)
typeof null                   
// 返回 object
```

6 种不同的数据类型：

- string
- number
- boolean
- object
- function
- symbol

### constructor 属性

**constructor** 属性返回所有 JavaScript 变量的**构造函数**。

```javascript
//使用 constructor 属性来查看对象是否为数组 (包含字符串 "Array"):
function isArray(myArray) {
    return myArray.constructor.toString().indexOf("Array") > -1;//
}
```

### 类型转换

[JavaScript 类型转换 | 菜鸟教程 (runoob.com)](https://www.runoob.com/js/js-type-conversion.html)



### typeof

变量的数据类型可以使用 **typeof** 操作符来查看：

```javascript
typeof "John"                // 返回 string
```

### 基础类型

1.字符串可以是引号中的任意文本，可以使用单引号或双引号

可以使用索引位置来访问字符串中的每个字符：

```javascript
var carname = "Volvo XC60";
var carname = 'Volvo XC60';
var character = carname[7];
```

使用内置属性 **length** 来计算字符串的长度：

```javascript
var txt = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";
var sln = txt.length;
```

字符串可以是对象String

```javascript
var x = "John";              
var y = new String("John");
(x === y) // 结果为 false，因为 x 是字符串，y 是对象
```

字符串属性和方法
原始值字符串，如 "John", 没有属性和方法(因为他们不是对象)。

原始值可以使用 JavaScript 的属性和方法，因为 JavaScript 在执行方法和属性时可以把原始值当作对象。

#### 模板字符串

模板字符串使用反引号 **``** 作为字符串的定界符分隔的字面量。

允许多行字符串、带嵌入表达式的字符串插值 和 一种叫带标签的模板 。

```javascript
//多行字符串
const multiLineText = `
  This is
  a multi-line
  text.
`;//浏览器可直接多行输出，无需加转义字符
//带嵌入表达式的字符串插值
const name = 'Runoob';
const age = 30;
const message = `My name is ${name} and I'm ${age} years old.`;
//一种叫带标签的模板
//模板字符串当作 HTML 模板使用：
let header = "";
let tags = ["RUNOOB", "GOOGLE", "TAOBAO"];
let html = `<h2>${header}</h2><ul>`;
for (const x of tags) {
  html += `<li>${x}</li>`;
}
html += `</ul>`;
```

**${name}** 和 **${age}** 是模板字符串的表达式部分，它们被包含在 `${}` 内部，并在运行时求值。



2.布尔（逻辑）只能有两个值：true 或 false。

3.Undefined 这个值表示变量不含有值。

4.可以通过将变量的值设置为 null 来清空变量。

### 对象

对象由花括号分隔。在括号内部，对象的属性以名称和值对的形式 (**name : value**) 来定义。属性由逗号分隔：

```JavaScript
var person={
firstname : "John",
lastname  : "Doe",
id        :  5566
};
//对象属性有两种寻址方式：
name=person.lastname;
name=person["lastname"];
```

定义方式：

```javascript
var carname=new String;

var person = {firstName:"John", lastName:"Doe", age:50, eyeColor:"blue"};
```

## 函数

**函数是对象**
在 JavaScript 中使用 typeof 操作符判断函数类型将返回 "function" 。

但是JavaScript 函数描述为一个对象更加准确。

JavaScript 函数有 **属性** 和 **方法**。

定义方式：

```javascript
function functionname(var1,var2)
{
    // 执行代码
}
```

特殊：

toString() 方法将函数作为一个字符串返回:

```javascript
function myFunction(a, b) {    return a * b;}
var txt = myFunction.toString();//txt=“function myFunction(a, b) {    return a * b;}”
```

### 箭头函数

```javascript
(参数1, 参数2, …, 参数N) => { 函数声明 }
(参数1, 参数2, …, 参数N) => 表达式(单一)
// 相当于：(参数1, 参数2, …, 参数N) =>{ return 表达式; }
// 无参时：() => {函数声明}

//示例
// ES5
var x = function(x, y) {
     return x * y;
}
 
// ES6
const x = (x, y) => x * y;
//等价于
const x = (x, y) => { return x * y };
```



### arguments 对象

JavaScript 函数有个内置的对象 arguments 对象。

arguments 对象包含了函数调用的**参数数组**。

```javascript
x = sumAll(1, 123, 500, 115, 44, 88);
 
function sumAll() {
    var i, sum = 0;
    for (i = 0; i < arguments.length; i++) {
        sum += arguments[i];
    }
    return sum;
}
```

### 作为函数方法调用函数

在 JavaScript 中, 函数是对象,有它的属性和方法。

**call()** 和 **apply()** 是预定义的函数方法。 两个方法可用于调用函数，两个方法的第一个参数必须是对象本身。

```javascript
function myFunction(a, b) {
    return a * b;
}
myObject = myFunction.call(myObject, 10, 2);     // 返回 20
//示例2
function myFunction(a, b) {
    return a * b;
}
myArray = [10, 2];
myObject = myFunction.apply(myObject, myArray);  // 返回 20
```

第一个参数就是设置this指向



## 事件

以按钮点击事件为例

onclick为按钮点击事件，赋值可以是**函数名**或者是**js语句**的**文本**

```javascript
//js语句
<button onclick="getElementById('demo').innerHTML=Date()">现在的时间是?</button>
//函数名
<button onclick="displayDate()">现在的时间是?</button>
```

常见事件

| onchange    | HTML 元素改变                        |
| ----------- | ------------------------------------ |
| onclick     | 用户点击 HTML 元素                   |
| onmouseover | 鼠标指针移动到指定的元素上时发生     |
| onmouseout  | 用户从一个 HTML 元素上移开鼠标时发生 |
| onkeydown   | 用户按下键盘按键                     |
| onload      | 浏览器已完成页面的加载               |

## 运算符

| 运算符 | 描述                                               | 比较           | 返回值  |
| ------ | -------------------------------------------------- | -------------- | ------- |
| ==     | 等于                                               | x==8           | *false* |
|        |                                                    | x==5或者x==‘5‘ | *true*  |
| ===    | 绝对等于（值和类型均相等）                         | x==="5"        | *false* |
|        |                                                    | x===5          | *true*  |
| !=     | 不等于                                             | x!=8           | *true*  |
| !==    | 不绝对等于（值和类型有一个不相等，或两个都不相等） | x!=="5"        | *true*  |
|        |                                                    | x!==5          | *false* |

### 特殊运算符

#### void

**`void` 运算符**对给定的表达式进行求值，然后返回 [`undefined`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/undefined)。

1.对表达式求值

2.返回undefined

语法：

```
void expression
//或者
void(expression)
```

示例：

```javascript
const output = void 1;
console.log(output);
// Expected output: undefined

void console.log('expression evaluated');
// Expected output: "expression evaluated"

void (function iife() {
  console.log('iife is executed');
})();
// Expected output: "iife is executed"

void function test() {
  console.log('test function executed');
};
try {
  test();
} catch (e) {
  console.log('test function is not defined');
  // Expected output: "test function is not defined"
}
//输出
> undefined
> "expression evaluated"
> "iife is executed"
> "test function is not defined"
```

特殊用法:

定义一个**死链接**请使用 `javascript:void(0)` 。(死链接毫无作用)

区别于`href="#"`表示位置

```javascript
<a href="javascript:void(0);">点我没有反应的!</a>
<a href="#pos">点我定位到指定位置!</a>
<br>
...
<br>
<p id="pos">尾部定位点</p>
```

注意用法：

```javascript
void 2 === "2"; // 相当于(void 2) === '2'，返回 false
void (2 === "2"); // 相当于void (2 === '2')，返回 undefined
```

参考：[void 运算符 - JavaScript | MDN (mozilla.org)](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/void)



## 循环

除了普通写法还有特殊写法(类似于python迭代器）

```javascript
var person={fname:"Bill",lname:"Gates",age:56}; 
 
for (x in person)  // x 为属性名
{
    txt=txt + person[x];
}
```

## js标签

 JavaScript 代码块之前加上标签名和冒号即可对该代码块标记：

```javascript
label:
//代码块
```

通过标签引用，**break** （只有break可以）语句可用于跳出任何 JavaScript 代码块：

```javascript
cars=["BMW","Volvo","Saab","Ford"];
//标签+代码块
list: 
{
    document.write(cars[0] + "<br>"); 
    document.write(cars[1] + "<br>"); 
    document.write(cars[2] + "<br>"); 
    break list;//跳出当前标签对应的代码块，，下面的语句不会执行
    document.write(cars[3] + "<br>"); 
    document.write(cars[4] + "<br>"); 
    document.write(cars[5] + "<br>"); 
}
```

## 异常机制

和其他语言差不多

```javascript
function myFunction() {
  var message, x;
  message = document.getElementById("p01");
  message.innerHTML = "";
  x = document.getElementById("demo").value;
  try { 
    if(x == "") throw "值是空的";
    if(isNaN(x)) throw "值不是一个数字";
    x = Number(x);
    if(x > 10) throw "太大";
    if(x < 5) throw "太小";
  }
  catch(err) {
    message.innerHTML = "错误: " + err + ".";
  }
  finally {
    document.getElementById("demo").value = "";
  }
}
```



## 类

### this

总结：**this 永远指向最后调用它的那个对象**

特殊用法:

#### 单独使用 this

单独使用 this，则它指向全局(Global)对象。

在浏览器中，window 就是该全局对象为 [**object Window**]:

```javascript
"use strict";
var x = this;
```

#### 函数中使用this

在函数中，函数的所属者**默认绑定**到 this 上。

在浏览器中，window 就是该全局对象为 [**object Window**]:

严格模式下函数是**没有**绑定到 this 上，这时候 this 是 **undefined**。

```
function myFunction() {
  return this;//返回的是window对象
}
//严格模式
"use strict";
function myFunction() {
  return this;//undefined
}
```

#### 事件中的 this

在 HTML 事件句柄中，this 指向了**接收**事件的 HTML 元素：

```javascript
//效果：点击后按钮消失
<button onclick="this.style.display='none'">
点我后我就消失了
</button>
```

#### 显式函数绑定、隐式绑定、window 绑定、new 绑定

个人理解：函数有个隐藏形参this，call函数可以指定this指向哪里，默认的this是指向调用对象本身。

隐式：

```javascript
  const user = {
    name: 'Cherry',
    age: 27,
    getName() {
      console.log(`Hello, my name is ${this.name}`)
    }
  }

  user.getName()  // Hello, my name is Cherry
```

函数被调用时先看一看点号左侧。如果有“点”就查看**“点”左侧的对象，这个对象就是 this 的引用**。

在上面的例子中，user 在“点号左侧”意味着 this 引用了 user 对象。所以就好像 在 getName 方法的内部 JavaScript 解释器把 this 变成了 user。

所以，你可以得出这样的结论：使用对象来调用其内部的一个方法，该方法的 this 是指向对象本身的。这就是所谓隐式绑定，你也可以这样认为：JavaScript解释器在执行 user.getName()时，将其转化为了：

```javascript
user.getName.call(user);
```

例子2：

```javascript
const user = {
  name: 'Cherry',
  age: 27,
  getName() {
    console.log(`Hello, my name is ${this.name}`)
  },
  mother: {
    name: 'Susan',
    getName() {
      console.log(`Hello, my name is ${this.name}`)
    }
  }
}

user.getName()  // Hello, my name is Cherry
user.mother.getName()    // Hello, my name is Susan//this指向mother
```

正如刚才所说：**this 永远指向最后调用它的那个对象**，那么“点”左侧的对象即为后调用该方法的对象，this 指向该对象。

显式：

通过 call 来设置函数执行上下文的 this 指向，比如：

```javascript
function getName () {
  console.log(`Hello, my name is ${this.myName}`)
}

let user = {
  myName: 'Cherry',
  age: 27,
}
getName.call(user)  // Hello, my name is Cherry
```

 getName 函数内部的 this 已经指向了 user 对象。

后续参考：[javascript - 一次性搞懂 this、call、apply、bind - 「大前端Space」公众号 - SegmentFault 思否](https://segmentfault.com/a/1190000023741737)



