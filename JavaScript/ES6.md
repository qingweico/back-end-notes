# <font style = "color:purple">ES6</font>

[TOC]

## let和var的区别

### let块级作用域,而var则是全局作用域

```javascript
for(var i = 0;i < 10;i++){      
}
console.log(i);//10   如果是用let则会报错i未定义
```

```javascript
var a = [];
for(var  i = 0;i < 10;i++){
    a[i] = function(){
        console.log(i);
    };
}
a[6](); //10     若是用let则输出6 let仅在块级作用域有效
```

### let和const不存在变量提升,即使用变量之前必须声明变量,不然会报错

变量提升：即变量可以在声明之前使用.在javascript中并不是严格的自上而下是执行程序,它会将当前作用域所有变量的声明提到程序的顶部

```javascript
console.log(a);//undefined
var a;
console.log(b);// Cannot access 'b' before initialization
let b;
let temp;
console.log(temp); //undefined
```

注意变量提升只是将变量的声明提前,则变量的赋值则不会

```javascript
a = 'a';
var a;
console.log(a);//'a'
console.log(b);   //undefined
var b = 'b';
```

### 暂时性死区

如果块级作用域内存在let命令,那么这个变量就被绑定到该作用域,不会受到外部变量的影响

```javascript
if(true){
    tmp = "abc"; //Cannot access 'tmp' before initialization 在变量声明前不可以进行赋值操作
    let tmp;
    tmp = "abc";
} 
```

总之,在块级作用域内,使用`let`命令声明变量之前,该变量都是不可用的,这在语法上,称为“暂时性死区”（temporal dead zone,简称 TDZ）

### 不允许重复声明

let不允许在同一个作用域内重复声明一个变量

- 在块级作用域内

```javascript
if(true)
{
    let temp = 123;
    //let temp = "hello; 报错
    //var temp = true;   报错
}
```

- 在函数作用域内

```javascript
function func(args){
    let args;
}          //报错
function func(args){
    {
        let args;
    }
}         //不报错
```

ES5只有函数作用域和全局作用域 ,而没有块级作用域

- 内层变量会覆盖外部变量

```javascript
var temp = 123;
function f(){
 console.log(temp);
    if(false){
        var temp = "Hello World"; //存在变量提升                              
    }
}
f();      //undefined
----------------------------------------
/*var temp = 123;
function f(){
   var temp;
   console.log(temp);
    if(false){
         temp = "Hello World";                           
    }
}
f();  */
```

-   用于计数的循环变量泄露为全局变量

```javascript
let s = "Hello";
for(let i = 0;i < s.length;i++){
    console.log(s[i]);
}
console.log(i); //5
```

## const

被const修饰的复合类型的数据类型（引用类型的数据类型）所指向的内存地址不能再改变,而在内存地址中保存的数据是可以改变的,而基本数据类型的值不可再改变

```javascript
const a = [];
a.push("Hello World");
console.log(a.length);//1
a = [];               //报错
```

ES5 只有两种声明变量的方法：`var`命令和`function`命令。ES6 除了添加`let`和`const`命令,另外两种声明变量的方法：`import`命令和`class`命令。所以,ES6 一共有 6 种声明变量的方法。

## 顶层对象

```javascript
window.a = 1
=> var a = 1;
```

`var`命令和`function`命令声明的全局变量,依旧是顶层对象的属性；另一方面规定,`let`命令、`const`命令、`class`命令声明的全局变量,不属于顶层对象的属性。

```javascript
class A{
     static b = 1;
}
console.log(A.b);     //1
console.log(window.b);//undefined
```

## 数组的解构赋值

```javascript
let [a, b, c] = [1, 2, 3];
```

```javascript
let [head, ...tail] = [1, 2, 3, 4];
head // 1
tail // [2, 3, 4]

let [x, y, ...z] = ['a'];
x // "a"
y // undefined  如果解构不成功,则返回undefined
z // []
```

默认值

```javascript
let [x, y = 'b'] = ['a']; // x='a', y='b'
let [foo = true] = [];
foo // true

let [x = 1] = [undefined];
x // 1
//如果数组成员与undefined严格不相等默认值则不会生效 null !== undefinde
let [x = 1] = [null];
x // null
```

表达式的懒加载

```javascript
function f() {
    console.log('aaa');
}
let [x = f()] = [1];
console.log(x); //1 只有当x对应的数组成员为undefined时才会加载函数f()
```

## 对象的解构赋值

对象的解构与数组有一个重要的不同。数组的元素是按次序排列的,变量的取值由它的位置决定；而对象的属性没有次序,变量必须与属性同名,才能取到正确的值。

## 字符串的解构赋值

```javascript
const [a, b, c, d, e] = 'hello';
a // "h"
b // "e"
c // "l"
d // "l"
e // "o
```

```javascript
let {length : len} = "hello";
console.log(len); //5
```

## 模板字符串

```javascript
let name = "jack";
console.log(`hello ${name}`);//hello jack
```

##  Symbol

```javascript
let obj = {};
let name = Symbol('name').toString();  //注意括号里的参数一定要加,为了区分两个不一样的Symbol
let age = Symbol('age').toString();    //如果不加参数则返回一个Symbol
obj[name] = 'Lisa';
obj[age] = '21';
obj.color = "red";
obj.name = "007";
console.log(obj);                 //{Symbol(name): "Lisa", Symbol(age): "21", color: "red", name: "007"}
Object.getOwnPropertySymbols(obj) //[ Symbol(name), Symbol(age) ]
Reflect.ownKeys(obj)              //返回对象的属性所有类型的键名,包括普通键名
```

## 箭头函数

```properties
箭头函数有几个使用注意点：
（1）函数体内的this对象,就是定义时所在的对象,而不是使用时所在的对象。
（2）不可以当作构造函数,也就是说,不可以使用new命令,否则会抛出一个错误。
（3）不可以使用arguments对象,该对象在函数体内不存在。如果要用,可以用rest参数代替。
（4）不可以使用yield命令,因此箭头函数不能用作Generator函数。
```

es6允许为函数设定默认值

```javascript
function log(x = 12,y = "index") {
    console.log(x,y);
}
log();//12,index
```

函数的长度（length返回(从第一个开始)没有默认参数的长度）要是第一个参数有默认值,管不管后面参数有没有默认值,length都为0

```javascript
(function f(a,b,c = 1) {
 }).length;   //2
```

```javascript
(function foo(a=0,b,c) {
}).length     //0
```

函数默认的作用域

```javascript
let x = 1;
function f(x = 1 ,y = x) {
    console.log(y);
}
f();//1
let i = 1;
function fx(y = i) {   //在函数调用时,y的默认变量i在函数内部尚未生成,所以指向了全局变量i
    let i = 2;
    console.log(y);
}
fx();//1
```

## 对象的扩展

属性的简洁表示法

```javascript
let foo = "bar";
let baz = {foo};
console.log(baz);  
//等价于
let foo = "bar";
let baz = {foo:foo};
console.log(baz);
```

方法的简写

```javascript
let func={
    method(){
        return "这是ES6方法的简写";
    }
};
console.log(func.method());
let fun={
    method:function () {
        return  "这是ES6之前的写法";
    }
};
```

属性名表达式

```javascript
let propKey="foo";
let object={
    [propKey]:"true",    //这种方法定义对象的属性时要将属性名放在中括号之内
    ['a'+'bc']: 123
};    //输出 foo:"true",abc:123*/
//-----------------------注意属性名表达式不能与简洁表达式同时使用-------------------------
let propKey="foo";
let object={
    propKey
};           //输出 propKey:"foo"
```

## Class类

```javascript
class Person{
    constructor(x,y){
        this.x = x;
        this.y = y;
    }
    sum(){
        return this.x + this.y;
    }
}
let value = new Person(1,2);
console.log(value.sum(1,2));
```

为类体添加一个方法

```javascript
value.__proto__.newF = function () {
    console.log("这里是新创建的方法");
};
console.log(Object.is(new Person() ,new Person()));              //false
console.log(Object.is(new Person().newF() ,new Person().newF()));//true
```

## Set

```javascript
 let arr = [4,4,7,8,9,8];
 let set = new Set(arr);
 //let newArr=[...set];       //将Set结构转换为数组方式1
 let newArr = Array.from(set);//将Set结构转换为数组方式2
 console.log(newArr);
 for(let i = 0;i < set.size;i++){
     console.log(set.keys()); //获取set集合中所有的键
 }
 for(let i of set.values()){
     console.log(i);
 }
```

## Map

```javascript
let map = new Map([['name',"jack"],["age",23]]);
console.log(map.size);        //2
console.log(map.get('name')); //jack
console.log(map.entries());   //返回键值对的遍历器
console.log(map.forEach(function (key,value) {console.log(key,value);}));
console.log(map.values());   //[Map Iterator] { 'jack', 23 }
console.log(map.keys());     //[Map Iterator] { 'name', 'age' }
let array = [...map];        //将map结构转换为数组
console.log(array);          //[ [ 'name', 'jack' ], [ 'age', 23 ] ]
let  brr = [];     
for(let i of array){
    brr = [...i];            //扩展运算符会覆盖上一次的数据
    console.log(brr);        //[ 'name', 'jack' ]  [ 'age', 23 ]
}
for(let i of array){
    for(let j of i){
        brr.push(j);
    }
}
console.log(brr);            //[ 'age', 23, 'name', 'jack', 'age', 23 ]
```

### Map转换为对象

```javascript
function MapToObj(strMap) {
        let obj = Object.create(null);
        for(let [k ,v] of strMap){
            obj[k] = v;
        }
        return obj;
}
```

### 对象转换为Map

```javascript
function ObjToMap(obj) {
    let strMap = new Map();
    for(let k of Object.keys(obj)){
        strMap.set(k,obj[k]);
    }
    return obj;
}
```

## proxy方法

get方法用于拦截对某个属性读取的操作

```javascript
let person = {
    name: "jack"
};
let proxy = new Proxy(person,{get:function (target,property) {
    if(property in target){
        return target[property];
    }
    else {
        throw new ReferenceError("property\"" + property + "\"is not exist");  //引用错误
    }
}
                             });
console.log(proxy.name);
console.log(proxy.age);    //会抛出一个错误
```

set方法用于拦截对于某个属性修改的操作 

```javascript
let obj = {set: function (objFunc,prop,value) {
        if(prop === "age"){         //注意这里age一定要加""
            if(!Number.isInteger(value)) {
                throw new TypeError("错误信息:不是整数");
            }
            if(value > 100){
                throw new RangeError("错误信息:年龄大于100");
            }
        }
        objFunc[prop] = value;
    }
};
let person = new Proxy({},obj);
person.age = 12;
console.log(person.age);
person.age = "jack"; // 抛出错误
```

## apply()函数

apply方法用于函数的调用,call及apply操作 apply()方法可以接受三个参数,分别是目标对象,目标对象的上下文对象,以及目标对象的参数数组
```javascript
let twice = {
    apply(target,ctx,args){
        return Reflect.apply(...arguments)*2;
    }
};
function sum(n) {
    return n + 1;
}
let proxy = new Proxy(sum,twice);
console.log(proxy(6));               //14
console.log(proxy.call(null,5,6));   //12
console.log(proxy.apply(null,[7,8]));//16  
```

## hash()函数

has方法用来拦截hasProperty方法,即判断对象是否具有某个属性时生效

```javascript
let  handler = {
    has(target ,key){
        if(key[0] === "_"){
            return false;
        }
        return key in target;
    }
};
let target = {_prop:"foo",prop:"foo"};
let proxy_s = new Proxy(target,handler);
console.log("_prop" in proxy_s); 
```

## construct()

construct方法用来拦截new命令,可以接受两个参数 target：目标对象 args：构建函数的参数对象
construct方法返回的必须是一个对象

```javascript
let p = new Proxy(function (){},{
    construct: function (target,args) {
        console.log("called: " + args.join());
        return {value:args[0] * 10}
    }
});
console.log(new p(1).value);//called: 1   10
```

## deleteProperty()

用来拦截delete操作 如果这个方法抛出错误或返回false,则无法将该属性删除

```javascript
let handler = {
    deleteProperty(target, key) {
        initialize(key,"delete");
        return true;
    }
};
function initialize(key,action) {
    if(key[0] === '_'){
        throw  new Error("删除下划线开头上的报错");
    }
}
let target = {_prop: "foo"};
let p = new Proxy(target ,handler);
console.log(delete p._prop);      //抛出错误
```

## Reflect方法

该方法可以接受三个参数,第一个必须为对象类型,第二个为对象的属性,第三个为另一个对象

```javascript
/*get方法*/
let userObj = {
    name: "Lisa",
    age: 21,
    get userInfo(){
        return this.name + " " + this.age;
    }
};
console.log(Reflect.get(userObj,"name"));                //name要加""
let receiver = {
    name: "jack",
    age: 22,
};
console.log(Reflect.get(userObj,'userInfo',receiver));   //jack 22
/*当对象中存在get方法时 this会指向receiver参数的属性*/
```

```javascript
/*set方法*/
let obj = {
    name: "Lisa",
    age: 22,
    set userInfo(value){
        return this.name = value;
    }
};
Reflect.set(obj,"name","Mike");
console.log(obj.name);                 //Mike
/*当value为空时,表示删除属性的值*/
Reflect.set(obj,"age");
console.log(obj.age);                 //undefined
/*当对象中有set方法且有receiver时,该方法会指向receiver中,所以修改的是receiver中属性*/
```

## Iterator

```properties
for..in
适合遍历对象,不适合遍历数组,遍历得到是键名(key)
for...of
遍历得到的是值(value) 而且提供了遍历所有数据结构是统一操作接口 可以使用break continue语句
```

## Generator函数

首先Generator函数在形式上就是一个普通函数,但也有两点不同之处

- 在function 与函数名之间有一个*号
- 在函数体语句内部使用yield语句,定义不同的内部状态      

```javascript
function* generator() {
    yield "hello";
    yield "world";
    return "javaScript";
}
let result = generator();
console.log(result);  //该函数并不执行,而是返回一个指向内部状态的指针对象,也就是遍历器对象Object [Generator] {}
/*想要取出定义在Generator函数里的值,使用next方法*/
console.log(result.next());  //{ value: 'hello', done: false }
console.log(result.next());  //{ value: 'world', done: false }
console.log(result.next());  //{ value: 'javaScript', done: true }
for(let i of result){
    console.log(i);          //hello world  不包括return语句的内容
}
```

### Generator的应用

异步操作的同步化表达

```javascript
function* loadUI() {
    yield show();
    yield loadYield();
    yield hide();
    return "这里是return语句";
}
function show() {
    return "Loading...";
}

function loadYield() {
    return "Has being Loaded";
}
function hide() {
    return "Over";
}
 let loader = loadUI();
// 加载UI
console.log(loader.next());
// 卸载UI
console.log(loader.next());
console.log(loader.next());
console.log(loader.next());
```

## async函数

```javascript
async function fo() {
    return "这是async函数的返回值";
}
//async函数返回一个Promise对象--可以使用then方法添加回调函数
console.log(fo());
fo().then((args) => console.log(args));
//async函数return语句返回的值作为then方法回调函数的参数
```

### await命令

await操作符用于等待一个Promise对象,只能在async function内部使用

```javascript
function testAwait(x) {
    return new Promise(resolve => {
        setTimeout(()=>resolve(x),2000);
    });
}
async function f() {
    let x = await testAwait("Hello World");
    console.log(x);
}
f();  //2s后打印Hello World
```

在正常情况await后跟都是Promise对象,如果不是,回转换为一个已完成状态的Promise对象

```javascript
async function f1() {
    return await 123;
}
f1().then(v => console.log(v));    //123
```

await函数返回的Promise对象,必须等到内部的所有的await命令和Promise对象执行完状态才会发生改变,也就是说要等到await函数内部的异步操作在执行完才会执行then方法指定的回调函数

### async函数的错误处理

```javascript
async function f() {
    return new Promise((resolve,reject) => {
        throw new Error("出错了!");
    });
}
f()
    .then((v) => console.log(v))
    .catch(e => console.log(e))
```

## 严格模式

```properties
变量必须声明后再使用
函数的参数不能有同名属性,否则报错；
不能使用with语句
不能对只读属性赋值,否则报错
不能使用前缀0表示八进制数,否则报错
不能删除不可删除的属性,否则报错；
不能删除变量delete prop,会报错,只能删除属性delete global[prop]
eval不会在它的外层作用域引入变量
eval和arguments不能被重新赋值；
arguments不会自动反映函数参数的变化
不能使用arguments.callee
不能使用arguments.caller
禁止this指向全局对象
不能使用fn.caller和fn.arguments获取函数调用的堆栈
增加了保留字（比如protected、static和interface）
```

### ES6 声明变量的六种方法

ES5 只有两种声明变量的方法: var 命令和 function 命令

ES6 除了添加 let 和 const 命令; 还有 import 和 class 命令