# 目录

[TOC]

## 字符串

```javascript
let str = "hello world";
str = str.charAt(0); //"h" 获取索引为0的元素
```

```javascript
str = str.slice(1,6); "ello"     
=>
str = str.substring(1,6); "ello"  //获取索引1到6之间的元素，不包括6 ，且都不会改变原来的字符串
```

```javascript
str = str.toLowerCase(); //大写所有的字符
str = str.toUpperCase(); //小写所有的字符，且都不改变原来的字符串
```

```javascript
str = str.trim();//移出字符串前面的空格，不改变原有的字符串
```

```javascript
let year = new Date().getFullYear();  //得到当前年份;
let month = new Date().getMonth();    //得到当前月份;
let day = new Date().getDate();       //得到当前日期
```

```javascript
let arr = [];
arr = str.split("");//将字符串分割为字符数组，且不会改变原来的字符串
str;//"hello"
arr;//Array(5) [ "h", "e", "l", "l", "o" ]
```

```javascript
str.indexOf("o");    //返回字符在字符串中第一次出现的位置
str.lastIndexOf("i");//返回该字符最后一次出现的位置
```

```javascript
let str = "hello";
str.charCodeAt('h')；    //108  返回该字符在Unicode中对应的数字编码
String.fromCharCode(97); //a  返回该UniCode编码对应的字符
```

```javascript
/*字符串匹配*/
let str="Lisa Jim Tom";
str.match("Tom")//Tom    匹配成功则返回该子串，否则返回null
/*匹配正则*/
let regexp = /Jim/;
str.match(regexp);//Jim
```

```javascript
let str = "I love you";
str.fontcolor("Red");        //按照指定的颜色显示字符串
```

```javascript
/*字符串去重函数*/
function removeRepeat(str){
    let  arr = [];
    let array = str.split("");
    for(let i = 0; i < array.length; i++){
        if(arr.indexOf(array[i]) === -1){ //在arr中没找到则返回-1
            arr.push(array[i]);
        }
    }
    return arr.join("");
}
```

### slice()  substr() substring() 参数为负的情况

- slice参数为负的情况下将负的参数加上字符串长度 
- substr会将第一个负的参数加上字符串长度，第二个负的参数变为0 
- substring会将所有的负的参数变为0 