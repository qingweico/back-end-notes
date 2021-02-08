js小知识点：

- Number([]) Number(null)都为0  而Number({}) 和Number(undefined)为NaN


- js的基本包装类型有`String`,`Number`,`Boolean` 

  ```javascript
  typeof String; //object
  typeof Number; //object
  typeof Boolean //object
  ```

- ```javascript
  var number = new Object(5);
  typeof number;//Number
  ```

  ***把相应的基本类型传入到Object的构造函数中会转化为相应的基本包装对象*** 

  ---

  ```javascript
  typeof number.valueOf(); //number 
  ```

  ***`valueOf`函数会把相应的包装类型的数据转换为基本数据类型*** 

  ---

  ```javascript
  let userinfo = {username:"count",password:123456};
  typeof object.toString();//[object Object]
  ```

  ---

  ***注意在js中对象类型包括object类型和function类型（都是小写）***

  ***而Object类型则是由function创造出来的 所以Object也是function类型***

  ***但是如果new Object() 则是对象类型，通过Object的构造函数创建对象***

  ***Array Date String等等都是函数类型function 而使用它们创建的对象就是object类型***

  ##### slice()  substr() substring() 参数为负的情况

  - slice参数为负的情况下将负的参数加上字符串长度 
  - substr会将第一个负的参数加上字符串长度，第二个负的参数变为0 
  - substring会将所有的负的参数变为0 

  ### 内存泄漏（Memery Leak） 

    ###### 指程序中已分配的堆内存由于某种原因没有释放或者无法释放 ，造成系统内存的浪费，从而导致程序运行速度减慢甚至系统崩溃等严重后果

  #### 有关对数组的整体性操作的问题 

  - 将一个数组赋值给另外一个数组

    ```javascript
    var nums = [];
    for(let i:nums){
        nums[i]=i+1;
    }
    var samenums = nums;
    ```

  - push()和unshift()函数可一次性为数组添加多个数据

  - Array.splice(index,len,newElement); 当删除长度为0时 即len=0 可以为数组添加元素

  - Array.sort() ; 该方法默认是按字典顺序排序 使用时应该重写compare方法


  #### js中关于两类函数创建方式的区别

  ```javascript
  function F(){consile.log("Hello World")};
  var f = F;
  F = null;
  f();//Hello World;
  ```

  ***f变量和F函数在内存中的结构***

  ![1583920411082](C:\Users\周庆伟\AppData\Roaming\Typora\typora-user-images\1583920411082.png)

  ***当F函数指向为null后变量和F函数的内存地址***

  ![1583920442344](C:\Users\周庆伟\AppData\Roaming\Typora\typora-user-images\1583920442344.png)

---------------

js中函数本身也是对象，可以拥有自己的属性

```javascript
 function f(){}
 f.fee="Go"
 f.value = function(){
 console.log(this.value);
 }
 f.value(); //Go
```

#### 函数中如果没有return语句 则会返回undefined

***闭包是指有权访问另一个函数作用域中变量的函数，常见闭包的创建方式就是在另一个函数的内部创建函数***

