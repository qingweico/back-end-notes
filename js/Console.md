## console

```javascript
/*%c CSS格式的字符串  下面的例子显示为黄低红字*/
console.log( '%cThis text is styled!', 'color: red; background: yellow; font-size: 24px;');
```

```javascript
/*console.table()可以将其内容转换为表格形式输出*/
let languages = [
    { name: "JavaScript", fileExtension: ".js" },
    { name: "TypeScript", fileExtension: ".ts" },
    { name: "CoffeeScript", fileExtension: ".coffee" }
    ];
console.table(languages);
```

```javascript
/*console.count()用来计数，输出它被调用多少次*/
function count() {
    for (let i = 0;i <= 10;i++){
        console.count("我被调用了");
    }
}
count();
```

```javascript
/*console.dir()以易于阅读的方式打印其内容*/
console.log({"name":"jack","age":23});
console.dir({"name":"jack","age":23});
/*console.dirxml主要以目录树的方式显示DOM节点，如果参数不是DOM节点而是普通的js对象，则dir和dirxml等价*/
```

```javascript
/*条件不满足的话，不会中断程序，会抛出一个错误*/
console.assert(true ,"value");    
```

```javascript
console.clear()//清理控制台
```

```javascript
for(let i = 0;i < 5;i++){
    console.log(i);
    if(i === 2)  debugger;  //运行到此处 程序暂停并且自动打开脚本源码界面，等待进一步处理
}
```

```javascript
/*用于计时，判断一个程序执行完毕花费的具体时间*/
console.time("Array initialize");
let arr = new Array(10000);
for(let i = 0;i < arr.length;i++){
    arr[i] = i;
}
console.timeEnd("Array initialized");
```

