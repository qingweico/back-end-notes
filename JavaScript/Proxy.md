```javascript
 //--------------------------下面是一个简单的proxy拦截的例子--------------------------
    let obj={
        "names":"jack",
        "age":21,
        "color":"blue"
    };
    let proxy=new Proxy(obj,{
        get(target,propertyKey){
            if(propertyKey==="color"){
                return target[propertyKey];
            }
            else{
                return "我要属性为color的值,请重新输入";
            }
        }
    });
    console.log(proxy.color);  //blue
    console.log(proxy.age);  //我要属性为color的值,请重新输入
    //Proxy也可以作为其他对象的原型对象
    let newObj=Object.create(proxy);
   console.log(newObj.color);  //blue
    //----------------------------------同一个拦截器函数可以设置多个拦截操作----------------------------------
    let handle= {
        get(target, name) {
            if (name === "prototype") {
                return Object.prototype;
            }
            return "Hello " + name;
        },
        apply(target, ctx, args) {  //拦截Proxy实调用的操作如proxy(...args) proxy.call(...args)  proxy.apply(...args)
            return args[0];
        },
        construct(target, args) {  //拦截Proxy实例作为构造函数调用的操作  如new proxy(...args)
            return {value:args[1]};
        },
    };
    let f_proxy=new Proxy(function (x,y) {return x+y;},handle);//注意这里不能使用箭头函数 而且Proxy的P一定要大写
    console.log(f_proxy(1,2));                            //1
    console.log(new f_proxy(1,2));                       //2
    console.log(f_proxy.prototype===Object.prototype);   //true
    console.log(f_proxy.foo);                              //Hello foo
    //--------------下面一个例子使用get拦截实现数组读取负数的索引--------------
     function createArray(...elements) {
         let handel={
             get(target,propKey){
                 let index=Number(propKey);
                 if(index<0){
                     propKey=String(target.length+index);
                 }
                 return Reflect.get(target,propKey);
             }
         };
         let target=[];
         target.push(...elements);
         return new Proxy(target,handel);
     }
     let arr=createArray(1,2,5,55);
     console.log(arr[-1]);
    // ------------利用Proxy的读取属性(get)操作,转变为执行某个函数, 从而实现属性的链式操作-------------
    let pipe=(function () {
        return function (value) {
            let funcStack=[];
            let o_proxy=new Proxy({},{
                get(pipeObject,fnName){
                    if(fnName==="get"){
                        return funcStack.reduce(function (val,fn) {
                            return fn(val);
                        },value);
                    }
                    funcStack.push(window[fnName]);
                    return o_proxy;
                }
            });
            return o_proxy;
        }
    }());
    var double = n => n*2;                                              //此处let不能换成var
    var pow = n => n*n;                                                //此处let不能换成var
    var reverseInt= n => n.toString().split("").reverse().join("")|0;     //此处let不能换成var
    console.log(pipe(3).double.pow.reverseInt.get);
    //-----------------------下面的例子使用get拦截，实现生成各种dom节点的dom函数---------------------------
    //attribute是属性的意思 和property一样
    const dom=new Proxy({},{
        get(target,property) {
            return function (attr = {},...children) {
                const e1=document.createElement(property);
                for(let prop of Object.keys(attr)){
                    e1.setAttribute(prop,attr[prop]);
                }
                for(let child of children){
                    if(typeof (child)==="string"){
                        child=document.createTextNode(child);
                    }
                    e1.append(child);
                }
                return e1;
            }
        }
    });
    const e1=dom.div({},
        "Hello My Name is ",
        dom.a({href:".."},"jack"),
        ". I Like",
        dom.ul({},
        dom.li({},"The Web"),
        dom.li({},"Food"),
        dom.li({},"...actually that \'s it")
        )
    );
    document.body.append(e1);
```

