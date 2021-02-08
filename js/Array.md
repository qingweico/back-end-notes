# 数组

[TOC]

## 数组的静态方法

```javascript
Array.isArray([]);//用来判断参数是否为数组，弥补了typeof运算符的不足
```

```javascript
let str = "hello";
Array.from(str); //[ "h", "e", "l", "l", "o" ]
Array.from([1,2,3],(x)=>x*x); //[ 1, 4, 9 ]  
//如果参数为数组，则返回一摸一样的新数组
let arr = Array.of(12,12);
arr; // [12,12];
```

## 数组的实例方法

```javascript
let arr = [1,2,5,6];
console.log(arr.valueOf());      //返回该字符串的本身
```

```javascript
Object.getOwnPropertyNames(arr)  //用来遍历对象的所有属性，包括不可枚举属性
Array(4)
0: "0"
1: "1"
2: "2"
3: "length"
```

```javascript
arr.toString()                   //返回数组的字符串形式
```

```javascript
arr.join();//该方法会把数住转换为字符串和String.split()方法正好相反,如果数组成员是undefined和null使用join方法将会被转换为空串
```

```javascript
//call方法可以用于字符串或类似于数组的对象
Array.prototype.join.call("hello", "-") //h-e-l-l-o
let obj = {0:1,1:2,length:2};
console.log(Array.prototype.join.call(obj, "-"));//1-2
```

### concat()

concat()方法用于多个数组的合并 但不改变原数组 注意： 如果参数为数组结果仍然是一维数组 但是参数是对象的话 合并后并不会改变参数类型对象仍然是对象，数组的话就还是数组，不会改变维数，如果参数是其他类型的值，相当于push()直接在数组后面追加值.如果数组成员包括对象的话 concat方法会返回当前数组的一个浅拷贝,就是拷贝的是对象的引用

```javascript
let concatObject = {a:1};
let oldArray = [concatObject];
let newArray = oldArray.concat();
newArray[0].a = 2;
console.log(newArray[0].a); //2  改变新数组的值，原对象的值也会跟着改变
console.log(concatObject.a);//2  
```

```java
arr.reverse();              //该方法会改变原数组
```

### arr.slice()不会改变原数组

```javascript
let arr = [0,1,2,3,4,5,6];
arr = arr.slice(1,3);       //返回被删除的元素[1,2] 不包括右边界索引 相当于字符串函数substring
console.log(arr);
```

该方法的一个重要的应用就是把类似数组的对象转换为真正的数组

```javascript
Array.prototype.slice.call({0:"1",1:"2",length:2});
```

```javascript
document.getElementsByTagName("div");//本来该方法会返回HTMLCollection对象
```

```javascript
Array.prototype.slice.call(document.getElementsByTagName("div"));//直接会返回一个数组
```

```javascript
document.querySelectorAll("div");//会返回NodeList对象
Array.prototype.slice.call(document.querySelectorAll("div")); //会返回一个数组
```

### arr.splice(start,count,addElement1,addElement2.....)会改变原数组

该方法可以删除指定的元素，也可以添加元素.第二个参数为零，后面可以是要插入的数据（可以为多项）

```javascript
let arr = [0,1,2,3,4,5,6];
arr = arr.splice(1,3);  //返回被删除的元素。输出1，2，3 第一个参数为开始是位置，第二个参数为删除的长度，相当于字符串函数substr()
//添加元素
let arr = [0,1,2,3,4,5,6];
arr.splice(1,0,5);
console.log(arr);// [0, 5, 1, 2, 3, 4, 5, 6]
```

### arr.sort()会改变原数组

该方法默认的是按照字典升序排列的，也可以自定义函数排列

```javascript
//按照降序排列数组
arr.sort(function(a,b) {return b-a;})
```

### arr.join(separator?string) 方法

该方法将数组合并成字符串与`str.split()互为逆运算将字符串分割为字符数组` 而且这两者都不会改变原来的数组或者字符串

### arr.pop()和arr.shift()

两者都会改变原来的数组，且两者都不用加参数

### arr.push(可变参数)和arr.unshift(可变参数)

这两者同样都会改变原数组，且可以一次性向数组中添加多个参数

### copyWithin(target: number, start: number, end?: number): this;

```javascript
let arr = [1, 2, 3, 4, 5];
console.log(arr.copyWithin(0, -2, -1) ); //4,2,3,4,5
```

## 数组迭代器

### map() 

该方法会将数组所有的成员传入参数函数，并且把每次执行的结果组成一个新的数组返回,该方法方法接受一个函数作为第一个参数.该函数可以有3个参数 当前成员，当前位置，数组本身

```javascript
let temp = [1,2,5];
let now = temp.map((elem, index, array) => (elem * 2));//将数组元素翻倍
```

map()方法还可以接受第二个参数 用来绑定回调函数内部的this变量

```javascript
let temp = ["a","b","c"];
console.log([1,2].map(function (e) {
    return this[e];
}, temp)); //["b","c"] 注意这里如果不可以使用箭头函数
```

### forEach()

用法和map差不多，不过前者不是为了得到返回值，只是单纯的遍历数组，而后者只为了得到返回值.而且forEach并不会中断循环.这两者都会跳过数组中的空位

```javascript
let forArray = [];
[1,2,4].forEach(function (elem) {return this.push(elem * elem);}, forArray);
console.log(forArray);//[1,4,16]
```

### some()

```javascript
//Check if An Array Contains A Value
let names = ["name","jack","tom"];
let isFound = names.some(name => name === "jack");
if(isFound){
    console.log("%c 找到你了！","color:red");
}
```

### every()

```java
let Array = [4,5,4,89,-5];
Array.every(n => n < 0);  //false  对每个元素进行判断
```

### filter()

```javascript
let filter = [1,2,5,54];
console.log(filter.filter((x) => x > 3));//[5,54];
```

```javascript
let isEven = [4,5,8,9,12];
console.log(isEven.filter((elem,index,array) => (elem % 2 === 0)));//[4,8,12];
```

```javascript
//以可以绑定this变量
let boundArray = [1,2,5,6,6,87,94];
console.log(boundArray.filter(function (item) {
    if(item > this.MAX)
        return true;
}, {MAX:3}));
```

### reduce()  reduceRight()

```javascript
//reduce
let numbers = [1,2];
function sum(accumulator, currentValue,currentIndex,array){
    return accumulator + currentValue;
}
let initialVal = 10;
let result = numbers.reduce(sum, initialVal);
console.log(result);//13
```

作用都是依此处理数组成员的每个值，最终累计为一个值. 不同的是前者是从左到右处理，而后者是从右向左处理

```javascript
//下面的函数的功能是找出数组中最长的单词
function findLongest(entries) {
    return entries.reduce(function (longest,entry) {
        return entry.length > longest.length ? entry : longest;
    })
}
console.log(findLongest(["find","users","www"]));
```

### arr.find()

```javascript
let arr=[1,5,8,-5,4];
console.log(arr.find((n) => n > 0)); //1 返回数组中的第一个满足条件的元素
```

```javascript
let arr=[1,2,5,6];
arr.find(function (value,index,arr){return value > 2&&index > 0;});//5  可以接受3个参数
```

### 睡眠排序

```javascript
const original = [3,15,2,9,63,2,7,1];
const result = [];
original.forEach(n => setTimeout(() => result.push(n),n));
console.log(result);
```

## 总结

- js的迭代方法有every some filter map forEach

- forEach没有返回值 

- 数组的的length方法不只是可读 ，也可以修改 

```javascript
let array = [];
for(let i = 0;i < 10;i++){
    array[array.length] = i;
}
array;//0 1 2 3 4 5 6 7 8 9
```

### 迭代器方法

- forEach() 方法 接受以一个函数作为参数 无返回值 不生成新的数组

- every() 方法 接受一个***返回值为布尔类型的函数***，对于所有的元素该函数都返回true 则该方法返回true

- some()方法 和every() 方法一样 但是不同的是只要有一个元素返回true 该方法就返回true

- reduce()函数 累加器

```javascript
function add(runningTotal, currentValue){
    return runningTotal + currentValue;
}
var num = [1,2,3,4,5,6,7,8,9];
num.reduce(add, initialValue);//累加数组中所有元组的和，可以设定初始化的值
```

- map() 该方法会生成一个新的数组 其他与forEach() 方法一样

- filter() 该方法和every() 方法很像 但是只返回回调函数为true的元素

### 怎么删除数组中的元素

- 可以将要删除元素处的索引设置为null   但此时数组的长度不变
- 使用splice() 函数 所删除元素后面的元素的索引都减一 此时的数组的长度会变化

```javascript
//求阶乘
function factorial(num){
            if(num <= 1){
                 return 1;
            }
            else{                                     
                return num * arguments.callee(num - 1);          //已被弃用
            }
        }
```

### 将数组中所有单词变成大写的两种方式

```javascript
let namesSet = ["Tom","Sam","Jim","Jack","Lisa"];
let names = [];
namesSetOne.forEach(Element => (names.push(Element.toUpperCase())));
console.log(names);
```

```javascript
let namesSet = ["Tom","Sam","Jim","Jack","Lisa"];
namesSet = namesSetOne.map(Element => Element.toUpperCase());
console.log(namesSet);
```