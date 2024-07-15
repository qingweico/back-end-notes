# 3小时成为 TypeScript 大神

[TOC]

来源 https://wangdoc.com/typescrip

## 环境搭建

全局安装

```shell
 npm install -g typescript
```

```shell
tsc -v
```

查看帮助信息

```shell
tsc -h
```

编译

```shell
tsc app.ts
```

编译多个ts文件并合并为一个js文件

```shell
tsc app1.ts app2.ts  --outFile app.js
```

--outDir

编译结果默认都保存在当前目录，--outDir参数可以指定保存到其他目录

--target

指定编译后的 JavaScript 版本

ts-node 模块

## 类型

### any 类型

表示没有任何限制

### unknown 类型 

严格版的 any 类型

不同之处

- 不能直接使用(包括其方法和属性)
- 不能直接赋值给其他类型的变量(any 和 unknown 类型除外)
- 只能进行比较运算

类型缩小 : 将一个不确定的类型缩小为更明确的类型

### never 类型

该类型为空, 不包含任何值

其重要特点就是可以赋值给任意其他类型

底层类型: never

顶层类型: any 和 unknown 

## 类型系统

### 基本类型

JavaScript 语言将值分成8种类型(基本类型)

- boolean
- string
- number
- bigint
- symbol
- object
- undefined
- null

上面所有类型的名称都是小写字母, 首字母大写的在 JavaScript 语言中都是内置对象, 而不是类型名称

### 包装对象类型(装箱)

对基本类型使用 API 时会自动包装为对象类型

基本类型

- boolean
- string
- number
- bigint
- symbo

symbol 类型和 bigint 类型无法直接获取它们的包装对象(即`Symbol()`和`BigInt()`不能作为构造函数使用, 比如new Symbol())

### Object 类型

大写的`Object`类型代表 JavaScript 语言里面的广义对象

除了`undefined`和`null`这两个值不能转为对象, 其他任何值都可以赋值给`Object`类型

### object 类型

小写的`object`类型代表 JavaScript 里面的狭义对象

只包含对象、数组和函数, 不包括原始类型的值

### undefined 和 null

undefined和null既是值, 又是类型

作为值, 任何其他类型的变量都可以赋值为 undefined 或者 null

### 值类型

单个值也是一种类型,称为值类型

```ts
let x:'hello';

x = 'hello'; // 正确
x = 'world'; // 报错
```

TypeScript 推断类型时，遇到const命令声明的变量，如果代码里面没有注明类型，就会推断该变量是值类型, 如果赋值为对象，并不会推断为值类型

子类型可以赋值给父类型，但是父类型不可以赋值给子类型

eg

```ts
let x:5 = 5;
let y:number = 4 + 1;

x = y; // 报错
y = x; // 正确
```

## 联合类型 

指的是多个类型组成的一个新类型 使用符号`|`表示

```ts
let x:string|number;
```

### 交叉类型

指的多个类型组成的一个新类型(需要同时满足)，使用符号`&`表示

```ts
let x:number&string;
```

交叉类型的主要用途是表示对象的合成, 常常用来为对象类型添加新属性

eg

```ts
type A = { width: number };

type B = A & { height: number };
```

## type 命令

来定义一个类型的别名

eg

```ts
type User = {
    id: string;
    username: string;
}
```

增加代码的可读性, 使复杂类型用起来更方便

别名的作用域是块级作用域

tips: type 命令属于类型相关的代码，编译成 JavaScript 的时候，会被全部删除

## typeof 运算符

一个一元运算符，返回一个字符串，代表操作数的类型

在 ts 中 typeof 的参数只能是标识符，不能是需要运算的表达式， 且编译后 typeof  会被删除

### 块级类型声明

ts 支持块级类型声明，即类型可以声明在代码块（用大括号表示）里面，并且只在当前代码块有效

### 类型的兼容

在 ts 中， 如果类型`A`的值可以赋值给类型`B`，那么类型`A`就称为类型`B`的子类型（subtype）

可以使用父类型的地方，都可以使用子类型，但是反过来不行

eg

```ts
type T = number|string;

let a:number = 1;
let b:T = a;
```

## 数组

数据的两种写法

```ts
let a:Array<number> = [1, 2, 3];
let b:number[] = [1, 2, 3];
```

如果数组成员的类型比较复杂 可以写成下面的形式

```ts
let arr:(number|string)[];
```

因为 数组的成员数量可以动态变化,  TypeScript 不会对数组边界进行检查，越界访问数组并不会报错

TypeScript 允许使用方括号读取数组成员的类型

eg

```ts
type Names = string[];
type Name = Names[0]; // string
```

```ts
type Names = string[];
type Name = Names[number]; // string
```

### 数组的类型推断

```ts
// 类型推断空数组为 any[]
const arr = [];
```

如果数组更新时会自动更新类型推断,类型推断的自动更新只发生初始值为空数组的情况。如果初始值不是空数组，类型推断就不会更新

### 只读数组

```ts
// 不可对数组成员做操作
const arr:readonly number[] = [0, 1];
```

TypeScript 将readonly number[]与number[]视为两种不一样的类型，后者是前者的子类型(但是父类型不能替代子类型)

```ts
function getSum(s:number[]) {
}

const arr:readonly number[] = [1, 2, 3];
// Error
getSum(arr)
```

详细解释下什么是子类型和父类型

子类型继承了父类型的所有特征，并加上了自己的特征，所以子类型可以用于所有使用父类型的场合，反过来就不行 即子类型可以赋值给父类型， 但是父类型不可以赋值给子类型(类比Java中的子父类)

readonly关键字不能与数组的泛型写法一起使用

ts 提供了两个专门的泛型

```ts
const a1:ReadonlyArray<number> = [0, 1];

const a2:Readonly<number[]> = [0, 1];
```

读数组还有一种声明方法，就是使用const 断言

```ts
const arr = [0, 1] as const;
```

## 元组

元组（tuple）是 TypeScript 特有的数据类型, 即成员类型可以自由设置的数组，即数组的各个成员的类型可以不同

eg

```ts
const s:[string, number, boolean]
    = ['a', 2, true];
```

其与数组定义的区分为【成员类型写在方括号里面的就是元组，写在外面的就是数组】

使用元组时，必须明确给出类型声明, 不然会被自动推断为数组

元组成员的类型可以添加问号后缀（`?`），表示该成员是可选的

eg

```ts
let a:[number, number?] = [1];
```

注意: 所有可选成员必须在必选成员之后, 下面的示例则会报错

```ts
type tuple = [
    number?,
    number,
];
```

使用 使用扩展运算符 ... 可以表示不限成员数量的元组

eg

```ts
type NamedNums = [
    string,
    ...number[]
]; 
// [...number[]] => number[]
```

扩展运算符（`...`）用在元组的任意位置都可以，它的后面只能是一个数组或元组

eg

```ts
type NamedNums = [
    string,
    ...[number, string, ...number[], ...[boolean, string, number]]
];
```

如果不确定元组成员的类型和数量, 可以写成如下示例

eg

```ts
type Tuple = [...any[]];
```

可以为元组的成员添加成员名, 如下所示

eg

```ts
type Tuple = [
    id: number,
    username: string,
    gender: boolean
];

```

读取成员类型， 使用 []

eg

```ts
type Tuple = [string, number, Date];
type Age = Tuple[1]; // number
```

可以和数组一样, 使用 number 读取 返回所有数值索引的成员类型
eg

```ts
type Tuple = [string, number, Date];
type TupleEl = Tuple[number];// string|number|Date
```

### 只读元组

两种写法

```ts
type Tuple = readonly [number,  string]
```

```ts
type Tuple = Readonly<[number,  string]>
```

只读数组或者只读元组

```ts
let point = [3, 4] as const;
```

只读元组和只读数组一样

- 子父类型特性
- 只读元组不能替代元组

### 成员数量的推断

如果没有可选成员和扩展运算符，TypeScript 会推断出元组的成员数量（即元组长度）

```ts
function f(point: [number, number]) {
   console.log(point.length)
}
f([1, 2])
```

如果包含了可选成员，TypeScript 会推断出可能的成员数量

```ts
function f(point: [number, number?]) {
   if (point.length === 1 ||
       point.length === 2) {
   }
   console.log(point.length)
}
f([1, 2])
```

如果使用了扩展运算符，TypeScript 就无法推断出成员数量

### 扩展运算符与成员数量

#### 展开运算符

介绍下展开运算符...(spread operator)

用处: 可以用来展开数组、对象或其他可迭代对象

node 环境下

- 合并数组

```ts
const array1 = [1, 2, 3];
const array2 = [4, 5, 6];
const mergedArray = [...array1, ...array2];
console.log(mergedArray); // 输出: [1, 2, 3, 4, 5, 6]
```

- 复制数组

```ts
const originalArray = [1, 2, 3];
const copyArray = [...originalArray];
console.log(copyArray);
```

- 函数调用时传递可变数量的参数

展开字符串

```ts
const str = "hello";
const charArray = [...str];
console.log(charArray); 
```

#### 在 ts 环境下使用 展开运算符

经典案例

```ts
const arr = [1, 2, 4];
// 没问题 数组展开后将元素分别作为单独的参数传递
// console.log可以接受任意数量的参数
console.log(...arr); // 1 2 4
function sum(a: number, b: number) {
    return a + b;
}
// 报错
// TypeScript 会进行类型检查，并期望 sum 函数接收两个 number 类型的参数
// 因为 arr 的类型是 number[], 不能确定 arr 中的元素数量是否符合 sum 函数的期望，因此会报错
sum(...arr);
```

解决方案

- const arr: [number, number] = [1, 2];
- const arr = [1, 2] as const;

## symbol 类型

## 函数

## 对象

## interface

## 类

## 泛型

## Enum
