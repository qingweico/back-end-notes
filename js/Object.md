# 目录

[TOC]

## 对象

### 实例方法

Object实例对象，就是定义在Object原型对象上的方法，可以直接被Object实例直接使用

```javascript
Object.prototype.print = function () {
    console.log(this);
};
Object().print(); //Object {}
```

Object本身是一个函数，通过Object()可以把任何数据类型转换为对象
* 将原始数据类型包装为相应的包装对象是实例
* 当参数为空时，或者为undefined   null 返回的是空对象

instanceof运算符用来判断一个对象是否是某个构造函数的实例

```javascript
function isObject(value) {
    return value === Object(value);
}
isObject({}); //true
```

#### toLocaleString

toLocaleString返回本地字符串形式    Array 和Number Date用自己定义的toLocaleString形式,而与toString返回的值不一样 下面就是Date的一个实例对象例子返回不一样

```javascript
let date = new Date();
console.log(date.toString());      //Sat Jul 11 2020 10:32:40 GMT+0800 (中国标准时间) 
console.log(date.toLocaleString());//2020/7/11 上午10:32:40
```

### 构造函数

new Object(value)表示新生成一个对象，并且它的值是value,而Object(value)则是将value转换为一个对象,二者的语义是不同的
### 静态方法

#### Object.keys();

```javascript
Object.keys();                 //参数为一个对象，方法会返回一个由一个给定对象的自身可枚举属性组成的数组
```

#### Object.getOwnPropertyNames(); 

```javascript
Object.getOwnPropertyNames();  //用法和Object().keys()类似 但是该方法还会遍历不可枚举类型的属性.返回一个数组
```

#### Object.prototype.valueOf(); 

```javascript
Object.prototype.valueOf();    //返回该对象的值，默认的是返回其本身
```

#### Object.prototype.toString();

```javascript
Object.prototype.toString();   //返回该对象的字符串形式
```

对一个对象调用toString()方法时，会返回[object,Object]

```javascript
//可以对任何值调用toString()方法，而不会受自定义 toString()方法的影响
Object.prototype.toString.call({}); //[object Object]
Object.prototype.toString.call([]); //[object Array]
```

```javascript
//下面是一个比typeof运算符判断类型更准确的函数
function type(value) {
    let s = Object.prototype.toString.call(value);
    return s.match(/\[object (.*?)\]/)[1].toLowerCase();
}
console.log(type(null)); //null
```

#### Object.prototype.hasOwnProperty(property)

```javascript
Object.prototype.hasOwnProperty(property);//判断该对象是否具有该属性(property) 返回Boolean类型
```

#### Object.getOwnPropertyDescriptor()

```javascript
Object.getOwnPropertyDescriptor();//方法可以获取属性的描述对象，第一个参数是目标对象，第二个参数是一个字符串，为该对象的属性
```

```javascript
let obj = {"name":"jack"};
Object.getOwnPropertyDescriptor(obj,"name");
configurable: true
enumerable: true
value: "jack"
writable: true
```

```javascript
Object.prototype.print = function(){
    console.log(this);
}
console.log(Object.keys(Object.prototype)); //Array [ "print" ]
```

#### Object.defineProperty()

```
Object.defineProperty();
```

```javascript
let object = Object.defineProperty({},"p",{
    //get(){return "这里是get方法";},     //注意get方法和value不能同时定义 且get方法存在时，writable的值只能是false
    value: "我是属性p属性描述对象的value",  
    writable: true,                       //表示属性值是否可以改变
    enumerable: true,                     //表示该数属性是否可以遍历
    configurable: false                   //表示可配置性，false可以阻止操作改写该属性，包括删除该属性，或者该属性的属性                                          描述对象(但是value除外)
});                                       //以上都为Boolean类型，且默认都为true
```

```javascript
object.p;//"我是属性p属性描述对象的value"
```

```javascript
object.p = "我的值无法被外界修改";//当writable值为false时注意直接对目标属性赋值是无法修改的但是以下方式仍可以修改对之前value的覆盖
console.log(object.p);         //"我的值无法被外界修改"
```

```javascript
Object.defineProperty(object,"p",{value: "我的值可以被改变"});
console.log(object.p);         //"我的值可以被改变"}
```

```javascript
console.log(delete object.p);  //false  无法删除该属性
```

```javascript
let newObject = Object.defineProperties({},{
    p1:{value: "我是属性p1中值" ,enumerable:true},
    p2:{value: "我是属性p2中的值" ,enumerable:true},
    p3:{get(){return newObject.p1 + "---" + newObject.p2},
        enumerable:true,
        configurable:true}
});
console.log(newObject.p3);//我是属性p1中值---我是属性p2中的值 
console.log(newObject.propertyIsEnumerable("p3"));   //true  该方法用来判断对象中某个属性是否可以遍历
```

如果原型独对象的某个属性的属性描述对象writable的值为false，那么子对象将无法自定义这个属性

```javascript
let proto = Object.defineProperty({},"foo",{value: "我是属性foo的值",writable:false}); //这里是原型对象
let sub = Object.create(proto);                                                      //这里是创建proto的子对象
console.log(sub.foo);          //我是属性foo的值
sub.foo = "我是子对象foo属性的值";
console.log(sub.foo);          //我是属性foo的值
/*该属性值已被锁定，子对象无法修改  但是可以通过子对象属性描述对象对父类的覆盖，此时会完全忽视原型链*/
```

```javascript
let a = {};
Object.defineProperty(a ,'property',{
    value: "values",
    enumerable: false  //不可遍历
});
for(let key in a){
    if(a.hasOwnProperty(key))
        console.log(key);
}
console.log(Object.keys(a)); //Array []
/*enumerable为true则为以下结果
["property"]
0: "property"
length: 1*/
```

#### 存储器 相当与java中private修饰变量时使用get和set方法

```javascript
let o = {
    value:5,
    get p(){
        return this.value;
    },
    set p(value){
        if(value > this.value){return this.value = value;}
        else throw new Error("新的值必须大于5，请重新输入");
    }
};
o.p = 10;
console.log(o.p);      //10
console.log(o.p = 4);  //报错
```

```javascript
 let o = {
        value:5,
        p:{
            get()
            {
                return this.value;
            },
            set(value)
            {
                if(value > this.value){this.value = value;}
                else throw new Error("新的值必须大于5，请重新输入");
            }
        }
    };
    o.p = 10;
    console.log(o.p);       //10
    console.log(o.p.set(4));//4
```

#### 对象的拷贝

```javascript
let copy = function (newObj,oldObj){
    for(let property in oldObj){
        if(oldObj.hasOwnProperty(property)){
            newObj[property] = oldObj[property];
        }
    }
    return newObj;
};
```

若遇到含有存储器的对象，需要使用Object.defineProperty()方法来拷贝对象

```javascript
let extend = function(to, from){
   for(let property in from){
       if(!from.hasOwnProperty(property)) continue;
       Object.defineProperty(to,property,Object.getOwnPropertyDescriptor(from,property));
   }
   return to;
};
```

```javascript
let to = {};
extend(to,{foo:"我为foo属性的值",
    get(){return "我被修改成了" + this.foo},
    set (value){this.foo = value;}});
to.set("Hello World");
console.log(to.get()); //我被修改成了Hello World
console.log(to.foo);   //Hello World
```

注意get() 与 get foo()是不一样的 前者是对象中的一个普通的方法，而后者则是属性foo的属性描述对象中的存储器

### 控制对象状态

#### Object.preventExtensions() 

防止对象再添加其他的属性   但可以删除旧的属性    但仍可以改变属性的值

```javascript
let demoObj = {foo:"我是属性foo的值",id:7,country:"China"};
Object.preventExtensions(demoObj);
delete demoObj.id;
console.log(demoObj.id); //undefined  
console.log(demoObj);    //Object { foo: "我是属性foo的值", country: "China"}
```

```javascript
Object.defineProperty(demoObj,"p",{value:123});//报错 Uncaught TypeError: can't define property "p": Object is not extensible
```

#### Object.isExtensible()  

 检查是否可以向对象中添加新属性

#### Object.seal()

该方法无法向对象中添加新属性，也无法删除旧的属性  实质是把属性描述对象的configurable变为false 同样可以改变属性的值

#### Object.isSealed() 

检查对象是否使用了seal方法

```javascript
javalet sealObject={strength:100};
Object.seal(sealObject); 
Object.defineProperty(sealObject,"speed",{value:120,configurable:true});/ can't define property "speed": Object is not extensible
```

#### Object.freeze()  

 使对象无法添加新的属性，也无法删除旧的属性，而且也无法修改属性的值，使得该对象变成了常量

#### Object.isFrozen()

检查对象是否使用了freeze方法

#### 上面三种方法的局限性就是可以通过改变原型对象来给对象增加属性

```javascript
et protoObject = Object();
Object.freeze(protoObject);
let createObject = Object.getPrototypeOf(protoObject);
createObject.p = "value";
console.log(protoObject.p);  //"value"
console.log(protoObject);    //但就protoObject对象本身而言仍然是个空对象
console.log(Object.keys(createObject));   //Array [ "p" ]
```

还有一件事就是如果对象的属性仍然是对象的话那么这些方法只能冻结属性指向的对象，而无法冻结对象本身的内容，就像const一样，虽然不能改变变量的内存地址但是仍然可以会改变其内容