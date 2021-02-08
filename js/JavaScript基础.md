# 目录

[TOC]

## JavaScript基础

### JavaScript数据类型

- number
- string
- boolean
- null
- undefined
- object
- Symbol（ES6）

### typeof运算符

typeof运算符可以用来返回一个值的数据类型

```javascript
console.log(typeof 123);            //number
console.log(typeof "hello");        //string
console.log(typeof function () {}); //function
console.log(typeof []);             //object
console.log(typeof {});             //object 
console.log(typeof null);           //object
```

### 布尔值

js中布尔值为false的只有6个，其他的都为true 

- 0
-  false 
- null
-  undefined 
- "" 或者''(空串)
-   NaN 

### 数值

JavaScript 内部，所有数字都是以64位浮点数形式储存，即使整数也是如此。

#### 数值精度

```javascript
Math.pow(2, 53);      //9007199254740992     浮点数的大小范围在2的53次方到-2的53次方
```

#### 数值范围

```javascript
Math.pow(2, 1024); // Infinity
```

如果一个数大于等于2的1024次方，那么就会发生“正向溢出”，即 JavaScript 无法表示这么大的数，这时就会返回`Infinity`。

```javascript
Math.pow(2, -1075) // 0
```

如果一个数小于等于2的-1075次方（指数部分最小值-1023，再加上小数部分的52位），那么就会发生为“负向溢出”，即 JavaScript 无法表示这么小的数，这时会直接返回0。

```javascript
Number.MAX_VALUE;  //1.7976931348623157e+308
Number.MIN_VALUE;  //5e-324
```

#### 数值的表示法

当小数点前的数字多于21位或者小数点后的0的个数多于5个会采用科学计数发表示，其他均采用字面值

科学计数法

```javascript
0.0000003 // 3e-7
```

#### Math

```javascript
Math.trunc(2.2);//去掉小数部分
```

```javascript
Math.cbrt(8);//立方根
```

### NaN（非数字）

- 字符串解析成数字失败时

```javascript
5 - 'x' // NaN
```

- 不合法的数学运算

```javascript
Math.acos(2) // NaN
Math.log(-1) // NaN
Math.sqrt(-1) // NaN
```

- 0/0也会得到NaN
- NaN只是一种特殊的数值，并不是一种新的数据类型

```javascript
typeof NaN // 'number'
```

- `NaN`不等于任何值，包括它本身。

- `NaN`与任何数（包括它自己）的运算，得到的都是`NaN`。

### 与数值转换有关的全局方法

#### parseInt() 

将字符串转换为整数，如果参数不是字符串类型，则会先将参数先转换为字符串再转换为整数

如果参数的第一个字符不可以转换为数值(后面是数字的正负号除外)，那么直接返回NaN

```javascript
parseInt('abc') // NaN
```

如果参数带有非数值类型，则停止转换，返回已经转换好的数值

```javascript
parseInt('8a')   // 8
```

如果参数以`0o 0x`等进制符号开头则会以相应的进制转换为整数

```javascript
parseInt('0x10') // 16
```

#### parseInt()的进制转换

parseInt()还可以加第二个进制参数(范围是2~36)，表示指定参数的进制

```javascript
parseInt('1000', 2) // 8
parseInt('1000', 6) // 216
parseInt('1000', 8) // 512
```

```javascript
parseInt(0o11, 2) // NaN
//如果第一个参数不是字符串，则会先转换为字符串
parseInt(String(0o11),2);
parseInt('9',2);//数值9为非法二进制数，所以返回NaN
```

#### parseFloat() 

parseFloat用法和parseInt差不多，parseFloat会将空串转换为NaN，而使用Number结果则为0

#### isNaN()

用来判断参数是否是NaN，只对数值有效，若参数不是数值，则会先转换为数值

```javascript
function IsNaN(value) {
  return typeof value === 'number' && isNaN(value);
}
```

```javascript
function IsNaN(value) {
  return value !== value;
}
```

其中isNaN函数是将null和true和空串当作0来处理

### 字符串

#### Base64转码

- `btoa()`：任意值转为 Base64 编码
- `atob()`：Base64 编码转为原来的值

注意不适合非ASCII码的字符

`encodeURIComponent`() 将非ASCII字符转化为ASCII字符

### 对象

```javascript
Object.keys(obj);//查看一个对象本身的所有属性
delete obj.p;    //删除指定对象的属性 返回boolean类型·
```

#### 对象的for...in...方法

该方法会遍历对象所有的可以遍历的属性，包括自身的属性还包括继承的属性

for..in..方法配合Object.hasOwnPropety()方法可以排除掉继承的属性

### 函数

声明函数的三种方式

- 使用function直接声明

```javascript
function add (a, b){ return a + b;}
```

- 使用变量赋值的写法（当使用函数表达式声明一个函数时，如果函数加上函数名，函数名只会在函数体内有效）

```javascript
let add = function(a, b){return a + b;}
```

- 使用构造函数

```javascript
let add = new Function('a', 'b', 'return a + b');
```

当采用function直接声明函数时，和var声明变量一样存在变量提升，即函数名的提升

```javascript
f(); //不会报错
function f(){} 
```

```javascript
f(); //会报错
let f = function(){}
=>
var f;
f();
f = function(){}
```

```javascript
var f = function () {
  console.log('1');
}
function f() {
  console.log('2');
}
f() // 1
```

Number([]) Number(null)都为0  而Number({}) 和Number(undefined)为NaN

js的基本包装类型有`String`,`Number`,`Boolean` 

```javascript
typeof String; //object
typeof Number; //object
typeof Boolean //object
```

```java
var number = new Object(5);
typeof number;//Number
```

把相应的基本类型传入到Object的构造函数中会转化为相应的基本包装对象 

---

```javascript
typeof number.valueOf(); //number 
```

`valueOf`函数会把相应的包装类型的数据转换为基本数据类型 

注意在js中对象类型包括object类型和function类型（都是小写）

而Object类型则是由function创造出来的 所以Object也是function类型

但是如果new Object() 则是对象类型，通过Object的构造函数创建对象

Array Date String等等都是函数类型function 而使用它们创建的对象就是object类型

#### 内存泄漏（Memery Leak） 

指程序中已分配的堆内存由于某种原因没有释放或者无法释放 ，造成系统内存的浪费，从而导致程序运行速度减慢甚至系统崩溃等严重后果

  #### js中关于两类函数创建方式的区别

  ```javascript
  function F(){consile.log("Hello World")};
  var f = F;
  F = null;
  f();
  ```

  变量f和F函数在内存中的结构

![1583920411082](https://pic.downk.cc/item/5f33502214195aa594de1e90.png)

  当F函数指向为null后变量和F函数的内存地址

![1583920442344](https://pic.downk.cc/item/5f33502214195aa594de1e92.png)

---------------

js中函数本身也是对象，可以拥有自己的属性

```javascript
 function f(){}
 f.fee = "Go"
 f.value = function(){
 console.log(this.fee);
 }
 f.value(); //Go
```

#### 函数中如果没有return语句 则会返回undefined

闭包是指有权访问另一个函数作用域中变量的函数，常见闭包的创建方式就是在另一个函数的内部创建函数