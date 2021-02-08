# 目录

[TOC]

## 组件

- 创建组件

```javascript
const cpn =  Vue.extend({
template: `
<div>
    <h3>我是组件</h3>     
</div>`
})
```

- 注册组件

```javascript
 Vue.component('my-cpn',cpn);
```

- 使用组件

```vue
<div id="app">
    <my-cpn></my-cpn>
    <my-cpn></my-cpn>
    <my-cpn></my-cpn>
</div>
```

### 全局组件和局部组件

```html
<div id="app">
    <cpns></cpns>
    <cpns></cpns>
    <cpns></cpns>
</div>
<!--Unknown custom element: <cpns>-->
<div id="app1">
    <cpns></cpns>
</div>
```

```javascript
//全局注册即为父组件在所有vue实例中都可以使用
//在实例中注册的组件即为子组件，只可以在当前组件中使用
const cpn =  Vue.extend({
template: `
<div>
    <h3>我是组件</h3>     
</div>`
})
const app = new Vue({
	el:'#app',
	data:{
	},
	components: {
		cpns: cpn
    }
})
const app1 = new Vue({
    el: '#app1',
    data: {

    }
})
```

### 父组件和子组件

```html
<div id="app">
    <father></father>
</div>
```

```javascript
const son =  Vue.extend({
    template: `
    <div>
    	<h4>我是子组件</h4>
    </div>`
})
const father = Vue.extend({
    template: `
    <div>
    	<h3>我是父组件</h3>
    	<son></son>        
    </div>`,
        components: {
            son: son
        }
})

const app = new Vue({
    el: '#app',
    data: {
    },
    components: {
        father: father
    }
})
```

### 组件注册的语法糖

```javascript
//注册全局组件
Vue.component('my-cpn',{
    template: `
    <div>
        <h3>我是组件</h3>     
    </div>`
});
//注册局部组件
const app = new Vue({
	el:'#app',
	data:{
	},
	components: {
		cpns: {
            template: `
            <div>
                <h3>我是组件</h3>     
            </div>`
        }
    }
})
```

```javascript
//父组件和子组件 
const app = new Vue({
        el: '#app',
        data: {},
        components: {
            father: {
                template: `
                    <div>
                        <h3>我是父组件</h3>
                        <son></son>
                    </div>`,
                components: {
                    son: {
                        template: `
              <div>
                <h4>我是子组件</h4>
              </div>`
                    }
                }
            }
        }
    })
```

### 模板抽离的写法

```html
<template id="father">
    <div>
        <h3>我是父组件</h3>
        <son></son>
    </div>
</template>
```

```javascript
template: '#father'
```

### 模块化

#### CommonJS导出模块

```javascript
function sum(a,b){
    return a+b;
}
module.exports = {sum}
```

#### CommonJS导出模块

```javascript
let {sum} = require('./math.js');
```

#### ES6导出模块

```html
<!--type必须为module-->
<script src="src/app.js" type="module">
```

```javascript
function sum(a,b){
    return a+b;
}

//第一种方式
export {sum}
//导出变量
export let total = 10;
//导出类
export class Person{
    run(){
        console.log("running...");
    }
}
//导出函数
export function f(){
    console.log("f..");
}
```

#### ES6导入模块

```javascript
import {sum} from "./math.js";
import {total} from './math.js';
import {Person, f} from "./math.js";
```

#### export default

```javascript
//导出变量
const name = "names";
export default name;
//导出函数
export default function d() {
    return 'd()...';

};
//导入模块 可以起别名
import n from './math.js';
```

export default在同一个模块中通过不允许存在多个

#### 一次性全部导入模块变量

```javascript
import * as math from './math.js';
```

```javascript
console.log((math.sum(12, 34)));
console.log(math.total);
const p = new math.Person();
p.run();
math.f();
```

