```html
<body><div id="div">
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
```

```html
<div id="div" class="test">
    
</div>
div.classList.remove("test"); //移出div标签的class属性中的test值
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

#### 表单

```html
<form action="" method="get" name="form">
    <input type="text">
    <input type="password">
    <input type="submit" value="提交">
</form>
```

```javascript
let form = document.forms   //返回一个HTMLCollection集合 取得页面中所有表单，通过索引或者name来取得具体的表单
document.forms[name]
document.forms[0]           //返回具体表单具体内容的引用
form.elements;              //返回表单中所有元素的集合 是一个有序列表，可以通过索引或者name属性来选取特定的表单字段
form.element[1]             //<input type="password">
```



#### 文本域表单有两种分别为input输入框和textarea

**可以通过form.elements[name].value="" 来设置文本域的初始值**

