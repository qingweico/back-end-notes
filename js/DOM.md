# 目录

[TOC]

## DOM

```properties
Dom的最小组成单位是节点,文档的树形结构就是Dom树是由不同类型的节点组成,每个节点都可以看成
Dom树的叶子
节点的类型一共有7种:
          1: Document      整个文档树的顶层节点
          2: DocumentType  doctype标签
          3: Element       网页的各种HTML标签
          4: Attribute     网页元素的属性 <class="right">
          5: Text          标签之间或标签之间的文本
          6: Comment       注释
          7: DocumentFragment   文档的片段
浏览器提供了一个原生的节点对象Node 上面的者七种节点都继承了Node 因此有一些共同的属性和方法
浏览器提供原生document节点,代表整个文档, 而整个文档的第一层有两个节点,第一个是文档类型的节点(<!doctype html>)
而第二个节点正是<html>标签 其中<html>标签构成了树结构的根节点,其他html标签节点都是他的下级节点
       //文档节点        nodeType表示节点的类型 返回的是一个整数  //Node对象定义了几个常量对应这些类型的值
       document.nodeType===Node.DOCUMENT_NODE                         //9
       //元素节点
       Element.nodeType ===Node.ELEMENT_NODE                          //1
       //属性节点
       Attr.nodeType === Node.ATTRIBUTE_NODE                          //2
       //文本节点
       Text.nodeType === Node.TEXT_NODE                               //3
       //文档片段节点
       DocumentFragment.nodeType === Node.DOCUMENT_FRAGMENT_NODE      //11
       //文档类型节点
       DocumentType.nodeType === Node.DOCUMENT_TYPE_NODE              //10
       //注释节点
       Comment.nodeType === Node.COMMENT_NODE                         //8 
       //------------------------------------------------------------------------------------
       Node.prototype.nodeName()  //返回节点的名称
       Node.prototype.nodeValue() //属性返回一个字符串,表示当前节点本身的文本值 该属性可读写
       注意只有文本节点 和注释节点和属性节点 可以返回结果 其他类型一律返回null
       <div id="d">Hello World</div>
       let div=document.getElementById("d");
       div.nodeValue   //null
       div.firstChild.nodeValue // Hello World
       div是元素节点  返回null
       而div.firstChild 则是文本节点 
       //-----------------------------------------------------------------------------------
       Node.prototype.textContent            //返回当前节点和它所有后他节点的所有内容
       document.documentElement.textContent  //读取整个文档的内容
       //--------------------------------------------------------------------------------------
       Node.prototype.baseURI        //表示当前网页的绝对路径 否则返回null
       //可以使用HTML标签的<base>标签来改变该属性的值  
       Node.prototype.ownerDocument  //返回当前节点所在的顶层文档对象 即document对象 
       //document本身使用则返回null
       Node.prototype.nextSibling    //返回紧跟当前节点后面的第一个同级节点
       //该属性还包括文本节点和注释节点(<!--comment-->) 如果当前节点后面有空格 会返回内容为空的文本节点
       Node.prototype.previousSibling //返回当前节点前面的第一个同级节点 其它用法和next一样
       Node.prototype.parentNode      //返回当前节点的父节点 而对于一个节点来说它的父节点可能是三种类型
       //元素节点或文档节点或文档片段节点
       Node.prototype.parentElement  //返回当前元素父元素的节点
       Node.prototype.firstChild     //返回当前节点的第一个子节点   
       Node.prototype.lastChild      //返回当前节点的最后一个子节点
       Node.prototype.childNodes     //返回当前节点所有的子节点 以类似数组的对象形式(NodeList)
       //childNode 还会返回文本节点和注释节点 如果没有子节点 则返回一个空的NodeList集合
       Node.prototype.isConnected    //判断当前节点是否包含在文档中  返回一个布尔值
       var test = document.createElement('p');
       test.isConnected // false。
       //上述test节点虽然已经创建 但是未添加到文档中
       document.body.appendChild(test);
       test.isConnected // true
       Node.prototype.appendChild //接受一个节点对象作为参数 并作为最后一个节点插入到当前节点
       //如果插入一个已经存在的节点 那么会将该节点移动到当前节点的最后一位子节点
       Node.prototype.hasChildNodes //判断当前节点是否有子节点 注意该方法可以返回任意类型的节点
       function DOMComb(parent, callback) {
        if (parent.hasChildNodes()) {
            for (var node = parent.firstChild; node; node = node.nextSibling) {
                DOMComb(node, callback);
             }
          }
             callback(parent);
       }
       DOMComb(document.body, console.log);
       //遍历当前节点所有的后代节点
       Node.prototype.cloneNode //克隆节点 接受一个布尔类型的参数 如果为true的话 会复制子节点的所有属性
       //但是不会有监听事件和回调函数属性
       Node.prototype.insertBefore //插入父节点指定的位置 接受两个参数(newNode referenceNode)
       Node.prototype.removeChild //移出当前节点的子节点
       var divA = document.getElementById('A');
       divA.parentNode.removeChild(divA); //也可以移出当前节点
       Node.prototype.replaceChild //替换当前节点的一个子结点 (newNode,oldNode)
       Node.prototype.contains //判断文档中是否包含参数节点
       Node.prototype.normalize //合并节点内所有的文本节点  Text.splitText();的n逆方法
       NOde.prototype.getRootNode //返回当前文档的根节点 document
       //----------------------NodeList接口和HTMLCollection接口---------------------------
       NodeList接口可以包含各种类型的节点 而HTMLCollection只能包含HTMl元素节点
       NodeList接口的实例对象是类似数组的对象 可以使用length和forEach属性 但是毕竟不是数组
       不能使用push和pop方法 但是可以使用Array.prototype.slice.call()将类似数组的对象转换
       为真正的数组 NodeList接口实例对象中除了Node.childNodes返回的是一个动态集合 其他的都
       是静态集合
       NodeList.prototype.length //返回所包含实例节点的数量
       document.querySelectorAll() //返回一个NodeList集合
       NodeList.prototype.items() 接受一个整数作为参数 返回当前位置的成员 如果索引错误 
       返回null 省略参数则报错
       //-----------------------------------------------------------------------------
       HTMLCollection 只能包含元素节点 而且没有forEach方法 只能使用for循环来遍历
       该接口返回的都是动态集合
       //----------------------document对象--------------------
       document.createTextNode //创建一个文本节点
```

```html
<body>
<div id="div">
    <strong>我是div节点的第一个孩子节点</strong><br>
    <strong>我是div节点的第二个孩子节点</strong>
</div>
    <script>
        //返回一个div节点中所有的strong标签的NodeList集合
      document.getElementById("div").querySelectorAll("strong");  
        //返回div标签的所有子节点的数目
        document.getElementById("div").childElementCount
        //返回第一个子元素（不包括文本节点和注释节点	）
       document.getElementById("div").firstElementChild;
        //返回第一个子元素（如果父元素和子元素之间有空格或回车则会返回文本节点，得不到正确结果）
       document.getElementById("div").firstChild;
    </script>
</body>
```

```html
<div id="div" class="test"></div>
```

```javascript
div.classList.remove("test");                          //移出div标签的class属性中的test值
document.getElementById("div").removeAttribute("class")//直接移出div中的class属性
```

| 选取方法                                     | 返回类型                                  |
| -------------------------------------------- | ----------------------------------------- |
| document.getElementById("name-p");           | 拥有指定id的第一个对象的引用              |
| document.getElementsByName("name-p")[0]      | NodeList                                  |
| document.getElementsByTagName("p")[1]        | HTMLCollection                            |
| document.getElementsByClassName("name-p")[1] | HTMLCollection                            |
| document.querySelector("p")                  | 文档中匹配指定的CSS选择器的***一个元素*** |
| document.querySelectorAll("p")[0]            | NodeList                                  |

