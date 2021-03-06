# 目录

[TOC]

## Error

### js错误处理机制 

js原生提供Error构造函数 所有抛出的错误都是这个构造函数的实例

```javascript
let error = new Error("出错了");
console.log(error);  //出错了
```

### Error派生6大错误对象

- SyntaxError         语法错误
- ReferenceError   引用错误

 * RangeError          范围错误
 * TypeError             类型错误  调用对象不存在的方法会抛出该错误
 * URLError
 * EvalError             不再使用了

该六大类都为构造函数

### 自定义错误

```javascript
 function UserError(message) {
     this.message = message || "默认信息";
     this.name = "userError";
 }
/*让userError继承Error对象,然后就可以生成这种自定义的错误*/
 UserError.prototype = new Error();
 UserError.prototype.constructor = UserError;
 console.log(new UserError("这是自定义的错误"));
 /*对于javascript引擎来说 遇到throw 程序就终止了*/
```

### finally语句

```javascript
let count = 0;
function f() {
    try{
        return count;
    }finally {
        count++;
    }
}
console.log(f());  //0
console.log(count);//1
/*说明return语句是在finally之前就执行了 f函数只是等到finally代码执行完毕后再弹出*/
```

下面是一个很好的例子来说明   try catch finally 及return 之间的关系

```javascript
function g() {
    try {
        throw '出错了！';
    } catch(e) {
        console.log('捕捉到内部错误');
        throw e;
    } finally {
        //return false;
    }
}
try {
    g();                           //捕捉到内部错误  
} catch(e) {
    console.log('捕捉到内部错误');   //捕捉到内部错误
}
g();// 捕捉到内部错误     Uncaught"出错了"
/*捕捉到内部错误 
捕捉到内部错误 
捕捉到内部错误 
Uncaught 出错了！
若在finally语句中加上return语句则只会打印一句'捕捉到内部错误'*/
```

```properties
finally 后面的语句根本不会再执行的两种情况
   1: 当finally语句块中有 return语句时且此时没有错误被捕获
   2: 当有错误被捕获时,无论finally语句块中是否有return语句
```