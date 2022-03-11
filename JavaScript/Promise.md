# Promise

[TOC]

## 任务队列和事件循环

```properties
首先js只在一个线程上运行(主线程), 但不代表js只有一个线程, 而js引擎有多个线程, 其他线程都是在后台配合; js内部采用事件循环机制(Event Loop); js在运行时除了一个正在运行的主线程, js引擎还提供了一个任务队列(task queue), 里面放着各种需要当前程序处理的异步任务(其实根据异步任务的类型, 存在多个任务队列, 为了方便理解, 这里只存在一个任务队列), js会去执行所有的同步任务,等到全部的同步任务全部执行完,就会去看任务队列里的异步任务,而如果有满足的异步任务, 那么该异步任务就会进入主线程, 成为同步任务, 执行完后, 下一个异步任务再进入主线程开始执行, 一但任务队列清空,那么该程序也就结束了
```

## 异步操作的基本流程控制

### 嵌套回调

```javascript
/*六个回调函数的嵌套, 一共需要花费6s, 不仅写起来麻烦, 还容易出错, 还难以维护*/
function async(args, callback){
    console.log("参数为" + args + " 1秒后返回");
    setTimeout(function(){callback(args*2);},1000);
}
function final(value){
    console.log("完成 ",value);
}
async(1,function(value){
    async(2,function(value){
        async(3,function(value) {
            async(4,function(value) {
                async(5,function(value) {
                    async(6,final);
                });
            });
        });
    });
});
```

### 串行执行

```javascript
/*串行执行*/
function async(args, callback){
    console.log("参数为"+args+" 1秒后返回");
    setTimeout(function(){callback(args*2);},1000);
}
function final(value){
    console.log("完成 ", value);
}
let items = [1,2,3,4,5,6];
let results = [];
function series(item) {
    if(item){
        async(item,function(result){
            results.push(result);
            return series(items.shift());
        });
    }else{
        return final(results[results.length-1]);
    }
}
series(items.shift());
```

### 并行执行

```javascript
/*并行执行*/
/*所有异步函数同时执行, 耗时少, 但是占用系统资源, 6s的用时缩短至1s*/
function async(args, callback){
    console.log("参数为"+args+" 1秒后返回");
    setTimeout(function(){callback(args*2);},1000);
}
function final(value){
    console.log("完成 ",value);
}
let items = [1,2,3,4,5,6];
let results = [];
items.forEach(function (item) {
    async(item,function (result) {
        results.push(result);
        if(results.length === items.length){
            final(results[results.length-1]);
        }
    })
});
```

### 并行与串行的结合

```javascript
/*并行与串行的结合*/
/*下面代码最多运行2个异步任务, 达到了资源与效率的最佳平衡*/
function async(args, callback){
    console.log("参数为"+args+" 1秒后返回");
    setTimeout(function(){callback(args*2);},1000);
}
function final(value){
    console.log("完成 ",value);
}
let limit = 2;
let running = 0;
let items = [1,2,3,4,5,6];
let results = [];
function launcher(){
    while(running < limit&&items.length > 0){
        let item = items.shift();
        async(item, function(result){
            results.push(result);
            running--;
            if(items.length > 0){
                launcher();
            }
            else if(running === 0){
                final(results[results.length-1]);
            }
        });
        running++;
    }
}
launcher();
```

## 定时器

```properties
setTimeout()函数的第一个参数是所要推迟执行的函数或一段代码 注意这里的一段代码要以字符串的形式出现
第二个参数是推迟指行的毫秒数
还可以接受更多的参数 作为回调函数的参数
如果回调函数是对象的方法,那么此时方法中的this不再指向对象所在的环境,而是指向的全局环境
而如果非要指向对象环境,使用bind方法绑定即可
```

```properties
setInterval()用法和setTimeout()完全一致,只是前者会每隔一段时间就执行,也就是无限次的运行
```

```properties
clearInterval()和clearTimeout() 取消对象的定时器
```

### setTimeout()

```javascript
/*用户在每次输入文本后,会立刻将字符转换为大写*/
document.getElementById("input-box").onkeypress=function(){
    let that = this;
    setTimeout(() => {
        that.value = that.value.toUpperCase();
        console.log(that.value);  
    },0);
}  
```

```javascript
/*改变网页元素的背景颜色*/
let div = document.getElementsByTagName('div')[0];
let timer;
let i = 0x000000;
function func() {
    timer = setTimeout(func, 0);
    div.style.backgroundColor = '#' + i.toString(16);
    if (i++ === 0xFFFFFF) clearTimeout(timer);
}
timer = setTimeout(func, 0);
```

### sleep() 函数

```javascript
function sleep(ms){
    let start = Date.now();
    while((Date.now() - start) < ms){}
}
```

## promise

```javascript
Promise对象通过自身的状态来控制异步操作,Promise实例共有3种状态
 1: pending    异步操作未完成 
 2: fulfilled  异步操作成功
 3: rejected   异步操作失败
 (fulfilled和rejected统称为resolved(已定型,该状态已不能再发生改变))
let promise = new Promise(function(resolved,rejected){
    if("异步操作成功"){
        resolved(value);
    }else if("异步操作失败"){
        rejected(new Error());
    }
});
```

```javascript
/*返回一个实例,5秒后该实例的状态会变为fulfilled*/
function timeout(ms){
    return new Promise((resolve,reject)=>{
        setTimeout(resolve,ms,'done');
    });
}
console.log(timeout(5000));
```

```javascript
let promise = new Promise(function(resolve,reject){
    resolve('done!');
});
promise.then(console.log); //done!
```