```javascript
/*reverse String*/
function reverse(str){
   let arr = str.split("");
   arr.reverse();
   str = arr.join("");
   return str;
}
```

```javascript
/*palindromes*/
function palindromes(str){        
    let newStr = str.replace(/[a-zA-Z0-9]/g,"").toLowerCase();
    let arr = newStr.split("");
    arr.reverse();
    arr= arr.join("");
    return  arr === newStr;
}
```

```javascript
/*find the longest word*/
function findLongestWord(str) {
    let arr = str.split(" ");
    let  max = arr[0].length;
    for(let i = 1;i < arr.length;i++){
        if(max < arr[i].length){
            max = arr[i].length;
        }
    }
    return max;
}
```

```javascript
/*
 * @param {*} collection 数组对象
 * @param {*} source     若该对象中的键和值都存在数组对象中，则返回collection中拥有该键值对的对象
 */
function where(collection, source) {
    let arr = [];
    let keys = Object.keys(source);
    arr = collection.filter(function(item){
        for(let i = 0;i < keys.length;i++){
            if(!item.hasOwnProperty(keys[i])||item[keys[i]] !== source[keys[i]]){
                return false;
            }
        }
        return true;
    })
    return arr;
}
where([{ first: "Romeo", last: "Montague" },
       { first: "jack", last: null }, { first: "Tom", last: "Capulet" }], { last: "Capulet" });
//{first: "Tom" last: "Capulet"}
```

```javascript
/*将switch...case结构转换为对象结构*/
function toAction(action) {
    let actions = {
        "java": function () {
            return "java";
        },
        "javascript": function(){
            return "javascript";
        },
        "c++": function (){
            return 'c++';
        }
    };
    if(typeof (actions[action]) !== "function"){
        throw new Error("Invalid Action!")
    }
    return actions[action]();
}
```

```javascript
/*替换目标字符串，且替换后首字母小写*/
function myReplace(str, before, after) {
    if(before[0] === before[0].toUpperCase()){
        after = after[0].toUpperCase() + after.slice(1);
    }
    str = str.replace(before,after);
    return str;
}
myReplace("A quick brown fox jumped over the lazy dog", "jumped", "leaped");
```