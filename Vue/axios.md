### Axios post请求

http post请求主要有三种类型

- Content-Type: application/json

- Content-Type: multipart/form-data
- Content-Type: application/x-www-form-urlencoded

```javascript
axios.get("url", {
    params: {k1: "", k2: ""}
})
```

```javascript
axios.post("url", {k: "v"})
 // 或者
const obj = {k: ""};
axios.post("url", obj);

// 错误的写法
axios.post("url", {
    data
})
```

