```javascript
async function test1() {
    console.log('start test1');
    console.log(await test2());
    console.log('end test1');

}
async function test2() {
    console.log('test2');
    return 'return test2 value';

}
test1().then(r => {
    console.log("success");
});

console.log('start async');

setTimeout(() => {
    console.log('setTimeout');
}, 0);

new Promise((resolve, reject) => {
    console.log('promise');
    resolve();
}).then(() => {
    console.log('promise2');
});
```

```javascript
Array.prototype.myEvery=function (callback) {
    if(typeof callback === 'function'){
        for(let i = 0;i < this.length;i++){
            if(callback(this[i])){
                return false;
            }
        }
        return  true;
    }
    return true;
};
function a(callback){
    console.log("这是一个回调函数");
    callback();
}
function b(a,b,c){
    console.log('我是a中的回调函数');
}
// console.log(b(1));
// a(b);
let op=[a(b),3,6,'every',9,'each'];
op.myEvery(function (value) {
    console.log(value);
});
```

```javascript
// 防抖函数
function debounce(fn) {
    let timeout = null; // 创建一个标记用来存放定时器的返回值
    return function () {
        clearTimeout(timeout); // 每当用户输入的时候把前一个 setTimeout clear 掉
        timeout = setTimeout(() => { // 然后又创建一个新的 setTimeout, 这样就能保证输入字符后的 interval 间隔内如果还有字符输入的话, 就不会执行 fn 函数
            fn.apply(this, arguments);
        }, 1000);
    };
}

function sayHi() {
    alert('防抖成功');
}

let inp = document.getElementById('inp');
inp.addEventListener('click', debounce(sayHi)); // 防抖
```

```javascript
const sortByBits = function(arr) {
    let bit = [];
    for(let n of arr){ bit[n] = get(n);}
    arr.sort(function(a, b){
        return bit[a] === bit[b] ? a - b : (bit[a] - bit[b]);
    })
    return arr;

};
function get(n){
    let count = 0;
    while(n){
        n = n & (n - 1);
        count++;
    }
    return count;
}
let arr = [0,1,2,3,4,5,6,7,8];
console.log(sortByBits(arr));
let jsonString = '{"message": "<div onmouseover = \\"alert(\'gotcha!\')\\">hover here.</div>"}';
let obj = JSON.parse(jsonString);
document.body.innerHTML = obj.message;
function htmlEscape(json){
    let obj = JSON.parse(json);
    for(let prototype in obj){
        return obj[prototype].replace(/[<>"&]/g,function(match, pos, originalText){
            switch(match){
                case "<":
                    return "&lt";
                case ">":
                    return  "&gt";
                case "&":
                    return "&amp";
                case "\"":
                    return "&quot" ;  
            }
        })
    }
}

htmlEscape('{"message":"<div onmouseover=\\"alert(\'gotcha!\')\\">hover here.</div>"}')
//&ltdiv onmouseover=&quotalert('gotcha!')&quot&gthover here.&lt/div&gt
```

```javascript
function getPageName (){
    const url = window.location.pathname;
    let lastIndex = url.lastIndexOf("/");
    const pageName = url.substr(lastIndex+1, url.lastIndexOf("."));
    alert(pageName);
}
```

