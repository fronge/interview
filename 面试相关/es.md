## 基本概念
### 索引
### 切片
### 文档
### 节点
### 集群
### 分片
### 副本

## CURD
### 创建
```
# 创建一条数据
POST test_fran/_doc
{
  "user":"Li Jack",
  "msg":"this is message for you",
  "age":18
}
```
### 更新
```
# 更新一条数据 _id=3
PUT test_fran/_doc/3
{
  "user":"Liu Jack",
  "msg":"this is message for liu",
  "age":39
}
```
### 删除
```
# 删除 _id=Eo781YoBKzgwSmJGIgH_
DELETE  test_fran/_doc/Eo781YoBKzgwSmJGIgH_
```
### 查询
```
# 查询所有
GET _search
{
  "query": {
    "match_all": {}
  }
}

# 获取id=3
GET test_fran/_doc/3

# 批量id查询
GET test_fran/_mget
{
  "docs":[
    {"_id":1},
    {"_id":2},
    {"_id":3}
    ]
}

# 分页查询
GET test_fran/_search
{
  "query": {
    "match_all": {}
  },
  "from":1,
  "size":10
}

# 模糊匹配
GET test_fran/_search
{
  "query": {
    "match": {
      "user":"Jack"
    }
  },
  "size":50,
  "from":0
  
}

# 多字段匹配
GET test_fran/_search
{
  "query": {
    "multi_match": {
      "query":"mssage",
      "fields":["msg"]
    }
  }
}

# 获取字段mapping类型 
GET test_fran/_mapping

#全文检索 精准匹配
GET test_fran/_search
{
  "query": {
    "term": {
      "user.keyword":{
        "value":"Lu Jack"
      }
    }
  }
}

# 固定分词
GET test_fran/_search
{
  "query":{
    "match_phrase": {
      "msg": "this is"
    }
  }
}

# must 必须满足所有条件
GET test_fran/_search
{
  "query":{
    "bool":{
      "must":[
        {"match":{"msg":"this"}},
         {"match":{"msg":"message"}}
      ]
    }
  }
}

# 多条件匹配
GET test_fran/_search
{
  "query":{
    "bool":{
      "must":[
        {"match":{"msg":"this"}},
        {"match":{"msg":"message"}}
      ],
      "must_not": [
        {"match":{"msg":"liu"}}
      ]
    }
  }
}

# should
GET test_fran/_search
{
  "query":{
    "bool":{
      "should":[
        {"range":{"age":{
          "gt":10,
          "lt":19
        }}}
      ]
    }
  }
}

# constance_score 计算分数规则
GET test_fran/_search
{
  "query":{
    "constant_score": {
      "filter": {
        "bool":{
          "should":[
            {"range":{"age":{
              "gt":10,
              "lt":19
            }}}
          ]
        }
      },
      "boost": 1.6
    }
    
  }
}
```