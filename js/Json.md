# 目录

[TOC]

### 什么是JSON

- JSON是一种数据交换格式 

- JSON独立于编程语言 
- JSON全称是JavaScript Object Notation(JavaScript对象表示法) 
- JSON源于JavasSript的一个子集

**数据交换格式**就是用于在不同的平台或系统间交换数据的文本,而表示法(专业术语)就是一个用于表示诸如数字或者单词等数据的字符系统,而且json具有可移植性,即以一种双方系统都兼容的方式在平台间传递信息

### JSON的语法

```json
{
    "brand" : "Crocs",
    "color": "pink",
    "size": 9,
    "hasLaces" false
}
```

首先json是**基于JavaScript对象字面量**,而字面量就是对数据值的具体表示（指字面意思与其想要表达的意思是完全一致的值）,为什么是基于呢,因为对象里还可以包含函数,所以不仅可以创建对象来表示一个物体的属性,还可以创建函数来表示物体的行为,但是数据交换格式的核心是数据,所以json中并不会涉及到JavaScript对象字面量中的函数,json所基于的JavaScript对象字面量单纯是指对象字面量及其属性的语法表示,而这种属性的表示方法是通过名称-值对来实现的

- JSON并不是JavaScript对象字面量而是基于JavaScript对象字面量  
- JSON基于JavasScript对象子面量中属性的语法,但是并不包含于JavaScript对象字面量中函数相关的部分,也就是说JSON的属性的值不能是函数  
- JSON属性都必须使用双引号,单引号也不行 (***当值是字符串类型时也只能使用双引号***),而在js中属性可以使用单引号代替双引号,且属性不需要加引号
- JSON中属性的值可以是字符串 数字 布尔值 null 数组或是对象 ,json的值并不是总是需要被双引号包裹,当值为字符串时,必须使用双引号,而其他数据类型的值都不应该被双引号包裹
- JSON中的数字可以是整数、小数、指数、或负数
- JSON数据类型中没有undefined
- JSON文件使用.json扩展名

#### 不可以正常使用的代码

```json
{
    "promo": "Say "Bob is is best!". to him"
}
```

#### 可以正常使用的代码

```json
//需要使用反斜线对字符串进行转义
{
    "promo": "Say \"Bob is is best!\" to him."
}
{
    "locations": "C:\\Program Files"
}
```

#### 不可以通过验证的JSON

```json
{
    title: "This is my title.",
    body: "This is my body."
}
```

#### 不合法的JSON

```json
{
    'title': 'This is my title.',
    'body': 'This is my body.'
}
```

#### 合法的JSON

```json
{
    "title": "This is my title.",
    "body": "This is my body."
}
```

### 语法验证

[JSON Formatter & Validator](https://jsonformatter.curiousconcept.com/) 

[JSON Editor Online]( https://onlinejsoneditor.com/)

### 媒体类型

媒体类型包括

  - 互联网媒体类型
  - 内容类型  
  - “MIME“类型

使用”类型/子类型”来表示 比如常见的text/html

JSON的MIME类型式application/json

### 对象和数组的区别

对象是键值对构成的列表或集合 数组是值构成的列表或集合

数组中应该具有相同的数据类型

（虽然数组中具有不同的数据是合法的 但是在JSON中并不建议这么做。因为***JSON***是一种数据交换格式 而而如果将数据传递给一个不使用JavaScript的系统,那么在解析时很可能会出错）

### JSON Schema

数据交换的一种虚拟的"合同"

 ***作用*** :验证数据是否满足接收方的要求

- ***JSON验证器负责验证语法的错误,而JSON Schema负责提供一致性检验*** 
- ***JSON Schema是数据接受方的第一道防线 也是数据发送方节约时间 保证数据正确的好工具*** 
- ***JSON可以解决下列由有关一致性的问题：***
  1. 值的数据类型是否正确 
  2. 是否包含所需要的数据 
  3. 值的形式是不是我需要的

```json
{
    //声明一个schema文件,值为草拟版本的链接
    "$schema":"http://json-schema.org/draft-04/schema",
    //schema文件的标题
    "title": "Cat",
    //定义需要在json中包含的属性
    "properties": {
        "name":{
            "type": "string"
            "minlength": 3,
            "maxlength": 20
        },"age":{
            "type": "number",
            "description": "Your cat's age in years.",
            "minimum": 0
        },
        "declawed":{
            "type": "boolean"
        },
        "description":{
            "type": "string"
        }
    },
    "required":[
        "name",
        "age",
        "declawed",
    ]
}
```

---

#### 合法的JSON

```
{
    "name":"jack",
    "age":2,
    "declawed":false
}
```

#### required

含有required字段的Schema 要求required数组中的属性必须出现在JSON中 如果没有required字段的话

那么JSON为空也是合法的

#### 不可以通过验证的JSON

```json
{
    "name": "Fluffy the greatest cat in the whole  wide world",
    "age": -2,
    "declawed" false,
    "description": "Fluffy loves to sleep all day." 
}
```


### JSON的安全问题

####  跨站请求伪造(CSRF)(cross-site-request forgery)

是一种站点对用户浏览器信任而发起攻击的方式

```json
 [
       {
            "user":"v"
        },{
            "password":123456
        }
  ]
  
```

***上面的JSON虽然是合法的 但是却十分危险 因为它是可以执行的JavaScript脚本(使用script标签链接并使用) 被称为顶层JSON数组***

---

```json
{ 
    "info":
    [
        {
            "user":"v"
        },{
            "password":123456
        }
    ]
 }
```

***将数组放在对象种 使其成为非法的JavaScript 这样就不会被script标签加载***

**禁止使用GET请求数据 仅使用POST请求获取数据 这样黑客就无法通过URL或<script>标签链接到数据**

***也不要在JSON中使用顶层数组***

####  注入攻击(向网站注入恶意代码)

**跨站脚本攻击(cross-site scripting XSS)** 是注入攻击的一种 在使用JavaScript从服务器获取到一段JSON字符串时并将转为JavaScript对象时发生

```javascript
//bad code
let jsonString = "alert('this is bad')";
var myObject = eval('(' + jsonString + ')');
```

使用JSON.parse代替eva()函数

```javascript
let jsonString = '{"animal": "cat"}';
let myObject = JSON.parse(jsonString);
alert(myObject.animal);
```

**因为当从服务器上获取JSON数据时,你并不能控制你获得JSON是什么样子的 那,那么服务器本身或发来的JSON数据本人劫持 那么就可能运行恶意代码**

**eval()函数的问题是它会把传入的字符串无差别的执行,并不能识别恶意脚本**

***JSON.parse()函数只会执行JSON代码 因此使用JSON.parse()更安全***

#### 不规矩的JSON

```json
{
    "message":"<div onmouseover = \"alert('gotcha!')\">hover here.</div>"
}
```

```javascript
let jsonString = '{"message": "<div onmouseover = \\"alert(\'gotcha!\')\\">hover here.</div>"}';
let obj = JSON.parse(jsonString);
document.body.innerHTML = obj.message;
```

当鼠标每次移动到屏幕上的消息时会弹出警告框 而黑客则可以通过该脚本获取你这也页面上所有的私人信息

解决办法是将所有诸如<div>这样的HTML标签进行转码<div>转码之后就是`&lt;div&gt` 

```javascript
//solved
function htmlEscape(json){
    let obj = JSON.parse(json);
    for(var prototype in obj){
        return obj[prototype].replace(/[<>"&]/g,function(match,pos, originalText){
            switch(match){
                case "<":
                    return "&lt";
                case ">":
                    return  "&gt";
                case "&":
                    return "&amp";
                case "\"":
                    return "&quot" ;  
            };
        })
    }
}
htmlEscape('{"message":"<div onmouseover=\\"alert(\'gotcha!\')\\">hover here.</div>"}')
//&ltdiv onmouseover=&quotalert('gotcha!')&quot&gthover here.&lt/div&gt
```

### 总结

- 跨站请求伪造

  *指的是站点对用户浏览器的信任进行的攻击*

- 顶层JSON数组

  存在于JSON名称键值对之外的位于文档最顶层的JSON数组

- 注入攻击

  依赖于将数据注入到Web应用程序以方便恶意数据执行或编译的攻击

- JSON跨站脚本注入攻击

   通过截取或将站点中所使用的第三方代码更换为恶意代码,对站点进行的一种注入攻击

----

来源：[美]Lindsay Bassett著 JSON必知必会
