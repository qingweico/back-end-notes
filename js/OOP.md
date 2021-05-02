# 目录

[TOC]

## oop

javascript语言的对象体系不是基于类的,而是基于构造函数(constructor)和原型链(prototype)  一个构造函数可以生成多个实例对象

构造函数有一下两个特点

- 函数体内部使用this关键字,代表了所要生成的实例对象
- 生成对象是时候要使用new命令                   

```javascript
/*构造函数第一个单词首字母要大写*/
function Vehicle(){ 
    this.price = 100;
}
let vehicle = new Vehicle();
console.log(vehicle.price);//100
/*如果不使用new命令 此时会变成普通函数  不会生成实例对象 而此时this会指向全局对象*/
```

### new命令

为了保证构造函数与new命令一起使用,下面有两种解决办法

```javascript
/*第一种使用严格模式*/
function Student(name,age){
    'use strict';
    this.name = name;
    this.age = age;
}
Student(); //此时会报错
```

```javascript
/*第二种则是在在构造函数内部判断是否使用了new命令,如果没有则返回一个实例对象*/
function Student(name, age){
    if(!(this instanceof Student)){return new Student(name, age); }
    this.name = name;
    this.age = age;
}
```

```javascript
/*如果构造函数内部有return语句,且返回的不是对象,那么new命令则会忽略该return语句,返回this对象*/
function People(){
    this.age=21;
    return {age:22}
}
console.log(new People().age);   //22
```

```javascript
/*函数内部可以使用new.target命令判断该函数是否被new命令调用  new.target指向该函数*/
function f(){
    if(!new.target){
        throw new Error("请使用new命令");
    }
}
f();    //报错
```

```javascript
/*this关键字就是属性或方法当前所在的对象*/
let person = {
    name: "person",
    describe(){
        return "My Name is "+this.name; //这里的this.name相当于person.name        
    }
};
console.log(person.describe());
```

### 函数执行环境

```javascript
let func = function (){
    console.log("我是函数f");
};
let obj = {f:func};
obj.f();   //对象的环境下执行
func();    //单独执行 
```

```javascript
let f = function() {
    console.log(this.x); //this.x指代当前运行环境的x
};
var x = 2;                 //此处的var不能换成let,因为es6中let声明的变量不再具有window全局变量属性
let o = {
    foo:f,
    x:4
};
f();      //2 f函数单独执行,执行的环境为全局环境 this指的是window环境,所以this.x = window.x = 2
o.foo();  //4 o.foo()执行的环境为对象o,所以this指代是对象o,所以this.x指的是o.x = 4
```

### this使用场合

```javascript
全局环境下 this === window  即this指向顶层对象window
构造函数  this指的是实例对象
对象的方法
```

```javascript
let way = {
    get:function(){
        console.log(this); //该this会指向当前函数的运行环境即对象way
    }
};
way.get();//{get: ƒ}   this只能指向上一层的对象,而不能继承更多的层
```

```javascript
let mulObj = {
    f1: function(){
        console.log(this);
        let f2 = function(){
            console.log(this);
        }();
    }
};
mulObj.f1(); //第一层this指向了函数f1的运行环境即对象mulObj 而第二层this指向了window
=====>
    let temp = function(){
        console.log(this);
    }
    let mulObj = {
        f1: function(){
            console.log(this)
            let f2 = temp();
        }
    }
    mulObj.f1();
```

```javascript
/*解决办法是在第二层改用一个指向外层的this变量*/
let solveObj = {
    f1: function(){
        console.log(this);
        let that = this;
        let f2 = function(){
            console.log(that)
        }();
    }
};
solveObj.f1();  //{f1: ƒ} {f1: ƒ}
```

```javascript
/*在严格模式下,如果函数内部的this变量指向了window,就会报错*/
let count = 0;
let inc = function(){
     //'use strict'; 报错
    this.count++; ==> window.count++
};
inc();
console.log(count); //0
===========
var count = 0; ==> window.count = 0
let inc = function(){
     //'use strict'; 报错
    this.count++;
};
inc();
console.log(count); //1
------------
let count = {
    count:0
};
count.inc = function(){
    'use strict';
    this.count++;
};
count.inc();
console.log(count.count); //1
```

```javascript
/*计算两点之间的距离*/
let points = [{x:0,y:0},{x:3,y:4}];
points.dist=function(){
    let p1 = this[0];
    let p2 = this[1];
    let x = p2.x - p1.x;
    let y = p2.y - p1.y;
    return Math.sqrt(x * x + y * y);
};
console.log(points.dist());
```

### 避免数组处理方法中的this

```javascript
let Arr = {
    v: "Hello",
    p: ["jack","Tom"],
    f:function(){  //对象的属性值也可以是函数
        this.p.forEach(function(item){
            console.log(this.v + "---" + item);
        });
    }
};
Arr.f();//undefined---jack undefined---Tom
```

有两种办法解决上述问题

- 使用中间this变量作为跳板

```javascript
let solveArr={
    v:"Hello",
    p:["jack","Tom"],
    f:function(){
        let that = this;
        this.p.forEach(function(item){
            console.log(that.v + "---" + item);
        });
    }
};
solveArr.f();
```

- 直接在forEach函数中绑定this指向即可 即作为forEach函数的第二个参数

```javascript
let solveArr = {
    v:"Hello",
    p:["jack","Tom"],
    f:function(){
        this.p.forEach(function(item){
            console.log(this.v + "---" + item);
        },this);
    }
};
solveArr.f();
```

### 绑定this的三种方法

#### Function.prototype.call()

函数实例的方法用来绑定函数内部this的指向,即函数执行时所在的作用域,然后在指定的作用域内调用该函数

```javascript
let call = {};
let callFunction = function(){
    return this;
};
console.log(callFunction() === window);       //true  
console.log(callFunction.call(call) === call);//true  将当前函数的作用域绑定到call对象上
```

```properties
call函数的参数应该是一个对象,如果参数为空或者为null和undefined则默认传入全局对象
call函数可以传入多个参数,第一个参数为对象,而其他则是函数调用时所用的参数
call的另一个应用是调用对象的原生方法
```

#### Function.prototype.apply()

apply方法和call方法差不多,唯一的区别是参数的传入,第一个参数都是this所要指向的那个对象 而apply剩下的所有的参数都以一个数组的形式作为参数传入

```javascript
/*下面是利用apply方法找出数组元素最大值的例子*/
let maxValue = [4,87,9,9,-1,];
console.log(Math.max.apply(null,maxValue)); //87
```

```javascript
/*下面是使用apply方法将数组空元素转换为undefined*/
Array.apply(null,[1,,2])
```

```javascript
/*下面是将类似数组的对象转换为真正的数组*/
Array.prototype.slice.call();
Array.prototype.slice.apply()
```

```javascript
/*上述两者的区别是:没有区别 都必须有length属性*/
Array.prototype.slice.call({0:1,length:1}));
Array.prototype.slice.apply({0:1,length:1});
```

绑定回调函数的对象

#### Function.prototype.bind()

如果回调函数是对象的方法,那么此时方法中的this不再指向对象所在的环境,而是指向的全局环境
而如果非要指向对象环境,使用bind方法绑定即可

```javascript
    //绑定前
let beforeBindObj = Object();
beforeBindObj.f = function(){
    console.log(this === beforeBindObj);  //false
};
beforeBindObj.f();
$(document).ready(function () {
    $("#button1").on("click",beforeBindObj.f);
});
--------------------------------------->
    //绑定后
let afterBindObj = Object();             //true
afterBindObj.f = function(){
    console.log(this === afterBindObj)
};
let after = function(){
    afterBindObj.f.apply(afterBindObj);
};
$(document).ready(function () {
    $("#button2").on("click",after);
});
```

```javascript
var count = 0;
let counter={
    count:0
};
counter.inc = function(){
    this.count++;
};


counter.inc();               //此时this指向了counter对象
console.log(counter.count);  //1 count = 0;

let f = counter.inc;
f()                         //此时this却指向了window
console.log(count);         //1  counter.count = 0;

let bind = counter.inc.bind(counter);
bind();
console.log(counter.count); //counter.count = 1

```

```javascript
/*bind还可以接受更多参数*/
let add = function(x,y){
    return x * this.a + y * this.b;
};
let addObject = {
    a:2,
    b:2
};
let newAdd = add.bind(addObject,5,5);
console.log(newAdd());         //20
```

### 对象的继承

javascript的继承是通过原型对象(prototype)来实现的,而非是class类来完成

```javascript
function Animal(name){
    this.name = name;
     this.walk = function(){
        console.log(this.name + "--" + "can walking");
    }
}
new Animal("小白").walk();
Animal.prototype.color = "white";//可以被所有实例共享
```

原型链的终点是null,通过Object.getPrototypeOf()可以返回参数对象的原型

#### constructor()构造器属性

```javascript
let myArr = function(){};
/*Array()是实例对象*/
myArr.prototype = Array();
let mine = new myArr();
mine.push(1,2,3);
console.log(mine.constructor.prototype); //Array []
console.log(mine.constructor.name);      //Array
console.log(mine.length);                //3
console.log(mine instanceof Array);      //true
```

constructor()属性的作用是可以知道实例对象到底是哪个构造器生成的

#### 构造函数的继承

```javascript
Object.create()  /*创建实例对象 被创建的对象可以继承模板对象的属性和方法*/
```

```javascript
function Super(){
    this.get = function(){
        return 'super';
    }
}
Super.prototype.color = "red";
function Sub(){
    Super.call(this);  //第一步子类继承父类的实例
    this.prop = "sub";
}
Sub.prototype = Object.create(Super.prototype);  //第二步子类继承父类的原型
Sub.prototype.constructor = Sub;
console.log(new Sub().color);   //red
```

#### javascript多重继承

在js中不允许一个对象继承多个对象,但可以通过变通的方法来实现

```javascript
function M1(){
    this.hello = 'hello';
}
function M2(){
    this.world = 'world';
}
function S(){
    M1.call(this);
    M2.call(this);
}
//继承M1
S.prototype = Object.create(M1.prototype);
//在继承链上加上 M2
Object.assign(S.prototype,M2.prototype);
//指定构造函数
S.prototype.constructor = S;
let s = new S();
console.log(s.hello); //hello
console.log(s.world); //world
```

#### Object对象相关的方法

```javascript
# Object.getPrototypeOf()获取参数对象的原型,这是获取原型对象的标准方法
# Object.setPrototypeOf()接受两个参数,前者是现有对象,后者是原型对象,为参数对象设置原型
# new命令可以使用Object.setPrototypeOf()方法模拟
```

```javascript
let SetNewOrder = function(){
    this.foo = "foo"
};
let setObject = new SetNewOrder();
console.log(setNewOrder.foo); //foo
====>
let setObject = Object.setPrototypeOf({},SetNewOrder.prototype);//将空对象的原型设置为构造函数的prototype属性
SetNewOrder.call(setObject);//然后将构造函数内部的this兑对象绑定这个空对象
console.log(setObject.foo); //foo
```

```javascript
//只要实例对象处在参数对象的原型链上,就会返回true,除了直接继承null的对象
Object.prototype.isPrototypeOf(Object.create(null));//false
```

`Object.prototype.__proto`__

实例对象的`__proto__`属性 返回该对象的原型

```javascript
let A = {
    name:"张三"
};
let B = {
    name:"李四"
};
let get = {
    name: "name",
    getName: function(){
        console.log("Hello" + " " + this.name);
    }
};
A.__proto__ = get;
B.__proto__ = get;
A.getName(); // 张三
B.getName(); // 李四
```

原型链可以使用`__proto__`很直观的表示 但它是一个内部属性,不应该对使用者暴露 应该使用getPrototypeOf和setPrototypeOf对原型对象的读写操作

#### 获取实例对象obj的原型对象的方法有三种

- `obj.__proto__`
- obj.constructor.prototype
- 
  Object.getPrototypeOf(obj)

下面是一个例子(obj.constructor.prototype在改变原型对象会失效)

```javascript
let P = function(){};
let p = new P();
let C = function(){};
C.prototype = p;
C.prototype.constructor = C;
//加入 C.prototype.constructor = C;即可
let c = new C();
console.log(c.constructor.prototype === p); //true
console.log(p.constructor.prototype);
console.log(c.constructor.prototype);
console.log(p.constructor.prototype.constructor);
console.log(c.constructor.prototype.constructor);
//C的原型对象被改成了p 但实例对象c.constructor.prototype却没有指向p,所以在改变原型对象时,也要设置constructor的值,所以推荐使用第三种方法获取
```

#### Object.getOwnPropertyNames()

<font style="color:purple;font-size:20px">Object.getOwnPropertyNames() 返回参数对象本身的所有属性（包括可遍历和不可遍历的）,不包括继承的属性 而Object.keys() 只能返回可遍历的属性</font>

下面是一个获取参数对象所有的属性(包括本身的还时继承的,不可遍历的还是可以遍历的)

```javascript
function inheritedPropertyNames(obj){
    let props = {};
    while(obj){
        Object.getOwnPropertyNames(obj).forEach((p) => (props[p] = true));
        obj = Object.getPrototypeOf(obj);
    }
    return Object.getOwnPropertyNames(props);
}
console.log(Object.getOwnPropertyDescriptor(Date,'length'));//属性一定要加引号
console.log(Object.getOwnPropertyNames(Date));
console.log(Object.keys(Date));
console.log(inheritedPropertyNames(Date));
```

#### 对象的拷贝

拷贝后的对象应该和原对象具有相同的原型和相同的实例属性

```javascript
function copyObject(orig){
    let copy = Object.create(Object.getPrototypeOf(orig));
    copyPropertiesForm(copy,orig);
    return copy;
}
function copyPropertiesForm(target,source){
    Object.getOwnPropertyNames(source).forEach(function(propKey){
        let desc = Object.getOwnPropertyDescriptor(source,propKey);
        Object.defineProperty(target,propKey,desc);
    });
    return target;
}
```

下面是一种更简单的写法,是ES2017才引入的Object.getOwmPropertyDescriptors()

```javascript
function copy(orig){
    return Object.create(
        Object.getPrototypeOf(orig),
        Object.getOwnPropertyDescriptors(orig)
    );
}
```

### IIFE立即执行函数表达式

```javascript
let funModel = (function(){
    return {
        isWalking(obj){
            obj.walk  = function(){
                console.log("He is can walk");
            };
        },
        isFlying(obj){
            obj.Fly = function(){
                console.log("He is can fly");
            };
        }
    };
})();
let Bird = function(){};
let bird = new Bird();
funModel.isWalking(bird);//funModel此时会立即执行,返回一个对象
bird.walk();             //He is can walk
```

