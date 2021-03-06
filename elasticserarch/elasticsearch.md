### 创建一个索引

```shell
PUT /索引名/文档类型名(默认为_doc)/文档id
{请求体}
```

字符串类型

text和keyword

数值类型

long,Integer,short,byte, double,float, half float, scaled float

日期类型

date

布尔类型

boolean

二进制类型

binary

![image-20201106164323682](C:\Users\周庆伟\AppData\Roaming\Typora\typora-user-images\image-20201106164323682.png)

PUT /qingweico/test/1
{
  "name": "庆伟",
  "age": 21
}

POST /test1/user/4
  {
    "name": "box",
    "des": "this is box"
  }
  GET /test1/user/_search?q=name:box
PUT /userinfo/user/5
{
  "names": "小眼4",
  "age": 26
}
 GET /userinfo/user/_search?q=name:小眼


  GET /userinfo/user/_search
  {
    "query": {
      "match": {
        "name": "小眼"
      }
    },
    "_source": ["name"],
    "sort": {
      "age": {
        "order": "desc"
      }
    },
    "from": 0,
    "size": 2
  }
  GET /userinfo/user/_search
  {
    "query": {
        "bool": {
          "should":
          [
            {
              "match":{
              "name": "小眼"
            }
            },
           {
              "match": {
               "age": 23
            }
           }
          ]
        }
      }
  }


    GET /userinfo/user/_search
  {
    "query": {
        "bool": {
          "must":
          [
            {
              "match":{
              "name": "小眼"
            }
            },
           {
              "match": {
               "age": 23
            }
           }
          ]
        }
      }
  }


    GET /userinfo/user/_search
  {
    "query": {
        "bool": {
          "must_not":
          [
            {
              "match":{
              "name": "t"
            }
            },
           {
              "match": {
               "age": 23
            }
           }
          ]
        }
      }
  }

    GET /userinfo/user/_search
  {
    "query": {
        "bool": {
          "must":
          [
            {
              "match":{
              "name": "小眼"
            }
            }
          ]
          ,
          "filter": [
            {
              "range": {
                "age": {
                  "gte": 22,
                  "lte": 24
                }
              }
            }
          ]
        }
      }
  }
  GET _analyze
  {
    "analyzer": "ik_smart",
    "text": "大说"
  }

    GET _analyze
  {
    "analyzer": "ik_max_word",
    "text": "大说"
  }

     GET _analyze
  {
    "analyzer": "standard",
    "text": "方地缚术"
  }

     GET _analyze
  {
    "analyzer": "keyword",
    "text": "方地缚术"
  }
   GET /userinfo/user/_search
  {
    "query": {
      "term": {
        "name": "小眼"
      }
    }
  }

  GET /userinfo

  

  GET /userinfo/user/_search
  {
    "query": {
        "match": {
          "name": "小眼"
        }
      },
      "highlight": {
        "fields": {
          "name": {}
        }
      }
      
  }
  GET /userinfo/user/_search
  {
    "query": {
        "match": {
          "name": "小眼"
        }
      },
      "highlight": {
        "pre_tags": "<p style='color:yellow'>", 
        "post_tags": "</p>", 
        "fields": {
          "name": {}
        }
      }
      
  }

  



   GET /userinfo/user/_search 
  {
  "query":{
    "match_all" : {}
  }
}