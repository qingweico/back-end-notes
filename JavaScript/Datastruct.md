## List

```javascript
function List() {
    this.listSize = 0;
    this.pos = 0;
    this.dataStore = [];
    this.clear = clear;
    this.find = find;
    this.toString = toString;
    this.insert = insert;
    this.append = append;
    this.remove = remove;
    this.front = front;
    this.end = end;
    this.prev = prev;
    this.next = next;
    this.length = length;
    this.currPos = currPos;
    this.moveTo = moveTo;
    this.getElement = getElement;
    this.contains = contains;
}

// append给列表添加一个元素
function append(element) {
    this.dataStore[this.listSize++] = element;
}

// remove 从列表中删除元素
function find(element) {
    for (let i = 0; i < this.dataStore.length; i++) {
        if (this.dataStore[i] === element) {
            return i;
        }
    }
}

function remove(element) {
    let find = this.find(element);
    if (find > -1) {
        this.dataStore.splice(find, 1);
        --this.listSize;
        return true;
    }
    return false;
}

// length 列表中有多少个元素
function length() {
    return this.listSize;
}

// toString 显示列表的元素
function toString() {
    return this.dataStore;
}

// insert 向列表中插入一个元素
function insert(element, after) {
    let insertPos = this.find(after);
    if (insertPos > -1) {
        this.dataStore.splice(insertPos + 1, 0, element);
        ++this.listSize;
        return true;
    }
    return false;
}

// clear 清空列表中所有元素
function clear() {
    delete this.dataStore;
    this.dataStore = [];
    this.pos = this.listSize = 0;
}

// contains 判断给定的值是否包含在列表中
function contains(element) {
    for (let i = 0; this.dataStore.length; ++i) {
        if (this.dataStore[i] === element) {
            return true;
        }
    }
    return false;
}

// 遍历列表
function front() {
    this.pos = 0;
}

function end() {
    this.pos = this.listSize - 1;
}

function prev() {
    if (this.pos > 0) {
        --this.pos;
    }
}

function next() {
    if (this.pos < this.listSize - 1) {
        ++this.pos;
    }
}

function currPos() {
    return this.pos;
}

function moveTo(position) {
    this.pos = position;
}

function getElement() {
    return this.dataStore[this.pos];
}
//-----------------------------------------------------------------------
var name = new List();
name.append("小黑");
name.append("小红");
name.append("小兰");
console.log(name.dataStore);
console.log(name.listSize);
console.log(name.remove(2));
console.log(name.listSize);
console.log(name.dataStore);
name.front();
console.log(name.getElement());
name.insert("小红", "小兰");
console.log(name.dataStore);
name.clear();
console.log(name.dataStore);
//使用迭代器访问列表
for (name.front(); name.currPos() < 5; name.next()) {
    console.log(name.getElement());
}
for (name.end(); name.currPos() >= 0; name.prev()) {
    console.log(name.getElement());
}
```

## LinkedList

```javascript
function Node(element) {
    this.element = element;
    this.next = null;
}

function List() {
    this.head = new Node("head");
    this.find = find;
    this.insert = insert;
    this.remove = remove;
    this.display = display;
}

function find(item) {
    let currNode = this.head;
    while (currNode.element !== item) {
        currNode = currNode.next;
    }
    return currNode;
}
// 插入元素
function insert(newElement, item) {
    let newNode = new Node(newElement);
    let current = this.find(item);
    newNode.next = current.next;
    current.next = newNode;
}

// 移出元素
function remove(item) {
    let curNode = this.head;
    while (curNode.next != null && curNode.next.element !== item) {
        curNode = curNode.next;
    }
    if (curNode.next.next != null) {
        curNode.next = curNode.next.next;
    } else {
        curNode.next = null;
    }
}

// 显示链表的元素
function display() {
    let currNode = this.head;
    while (currNode.next !== null) {
        console.log(currNode.next.element);
        currNode = currNode.next;
    }
}

// Test
let cities = new List();
cities.insert("Beijing", "head");
cities.insert("shanghai", "Beijing");
cities.insert("Tianjin", "shanghai");
cities.insert("nanjing", "Beijing");
cities.insert("zhengzhou", "shanghai");
cities.insert("hainan", "Beijing");
cities.insert("hangzhou", "shanghai");
cities.remove("Tianjin")
cities.display();
```

## Queue

```javascript
function Queue(){
    this.dataStore = [];
    this.enqueue = enqueue;
    this.dequeue = dequeue;
    this.front = front;
    this.rear = rear;
    this.toString = toString;
    this.empty = empty;
}
function enqueue(element){
    return this.dataStore.push(element);
}
function dequeue(){
    return this.dataStore.shift();
}
function front(){
    return this.dataStore[0];
}
function rear(){
    return this.dataStore[this.dataStore.length-1];
}
function toString(){
    let str = '';
    for(let element of this.dataStore){
        str+=element + ' ';
    }
    return str;
}
function empty(){
    return this.dataStore.length === 0;
}
//--------------------------------------------------
let queue = new Queue();
queue.enqueue("java");
queue.enqueue("C#");
queue.enqueue("python");
queue.enqueue("Go");
console.log(queue.toString());
console.log(queue.empty());


//---------------使用队列进行基数排序-----------
function distribute(numbers,queue,n,digit){
    for(let i = 0;i < n;i++){
        if(digit===1){
            queue[numbers[i]%10].enqueue(numbers[i]);
        }
        else{
            queue[Math.floor(numbers[i]/10)].enqueue(numbers[i]);
        }
    }
}
function collect(queues,numbers){
    let i=0;
    for(let digit = 0;digit < 10;digit++){
        while(!queues[digit].empty()){
            numbers[i++] = queues[digit].dequeue();
        }
    }
}
function printArr(arr){
    let str = '';
    for(let element of arr){
        str+=element + ' ';
    }
    return str;
}
let queues = [];
for(let i =0;i <=10;i++){
     queues[i] = new Queue();
}
let numbers = [];
for(let i = 0;i < 10;i++){
    numbers[i] = Math.floor(Math.floor(Math.random()*101));
}
console.log(printArr(numbers));
//第一次排序的结果
distribute(numbers,queues,10,10);
collect(queues,numbers);
console.log(printArr(numbers));
//第二次排序的结果
distribute(numbers,queues,10,10);
collect(queues,numbers);
console.log(printArr(numbers));
```

## Stack

```javascript
function Stack() {
    this.dataStore = [];
    this.top = 0;
    this.push = push;
    this.pop = pop;
    this.peek = peek;
    this.length = length;
    this.clear = clear;
}
function push(element) {
    return this.dataStore[this.top++] = element;
}
function pop() {
  return this.dataStore[--this.top];
}
function peek() {
    return this.dataStore[this.top-1]
}
function length() {
    return this.top;
}
function clear() {
    this.top = 0;
}
// var stack = new Stack();
// stack.push("jack");
// stack.push("Tom");
// stack.push("Suren");
// stack.push("Lisa");
// stack.peek();
// console.log(stack.length());
// stack.clear();
// stack.push("小河");
// stack.push("小兰");
// stack.push("小六");
// stack.push("小red");
// console.log(stack.dataStore);
//-------------------------------------------------------------
//进制转换
function mulBase(num,base){
    var stack = new Stack();
    do{
        stack.push(num%base);
         num = Math.floor(num/base);
    }while(num>0);
    var result = "";
    while(stack.length()>0){
        result+=stack.pop();
    }
    return result;
}
console.log(mulBase(1024,8));
//判断给定的字符串是否是回文
function isPalindrome(word) {
    var stack = new Stack();
    for(let i= 0;i<word.length;i++){
        stack.push(word[i]);
    }
    var words = "";
    while(stack.length()>0){
        words+=stack.pop()
    }
    return word===words;
}
console.log(isPalindrome("catac"));
//使用栈模拟递归过程
function fact(num){
    var stack = new Stack();
    while(num>1){
        stack.push(num--);
    }
    var product = 1;
    while(stack.length()>0){
        product *=stack.pop();
    }
    return product;
}
console.log(fact(5));
```

