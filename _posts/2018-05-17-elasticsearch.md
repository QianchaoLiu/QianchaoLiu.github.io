--- 
layout: post 
title: "Elasticsearch" 
date: 2018-05-17 00:00:04 
categories: liuqianchao update
 ---


## ElasticSearch Query

#### from, size, 子列约束 

通过from设置从第几个结果开始
size则相当用limit
fileds:["subcolumn"]用来约束要返回的子列, 但默认情况下fileds的支持是不开启的，官方更建议使用“_source”:["subcolumn"]

```sql
{
  "from": 0,
  "size": 3,
  "_source": ["message"],
  "query": {
    "query_string": {
      "query": "message:bitcoin"
    }
  }
}
```

#### term

term要求完全匹配(and关系)传入的值。

term: 单条查询

```sql
{
    "query":{
        "term"：{
            "message": "bitcoin"
        }
    }
}
```

terms: 多条查询, 通过minimum_match确定至少符合条件的数目

```sql
{
    "query":{
        "term"：{
            "message": ["bitcoin", "ethereum"],
            "minimum_match":1
        }
    }
}

```

#### match查询

不同于term的完全匹配，match默认对字符串是分词后部分匹配(对数字、时间、bool或者not_analyzed字段仍是精确匹配），并根据tf-idf值排序后返回结果, 可以使用operator该修改or的逻辑：
match使用operator来设置匹配模式：and/or

```SQL
{
  "query": {
    "match": {
      "message": {
        "query": "keyword1 keyword2",
        "operator": "and"
      }
    }
  }
}
```

使用slop匹配缺失值, slop为缺省内容的数目, 下面的语句会返回所有keyword1 xxx xxx keyword4

```SQL
{
  "query": {
    "match": {
      "message": {
        "query": "keyword1 keyword4",
        "slop": 2
      }
    }
  }
}
```

使用multi_match，来对不同的字段，使用相同的规则, 只要有一个字段满足条件就会返回数据

```sql
{
    "multi_match":{
        "query" : "bitcoin"
        "fields": ["title", "body"]
    }
}
```


#### bool

所有符合条件都需要使用bool语句

- "must":  # 必须满足
- "should":  #至少满足一个
- "must_not": 不满足


```python
{
    "query":{

        "filtered":{
            "filter" :{
                
                    "bool":{
                        "must":{
                            "term":{
                                "message": "bitcoin"
                                }
                        },
                        "should": {
                            "range":{
                                "timestamp": {"gte": 15, "lte": 20}
                            }
                        },
                        "must_not": {
                            "term": {
                                "message": "eos"
                            }
                        }
                    }

            }
        }




    }
}
```


#### exists, missing

相当于sql中的is not null和is null

```sql
{
    "exists": {
        "field": "title"
    }
}

```

#### filter

上面我们写了很多条件筛选的语句，那么为什么这还需要`filter`关键词？这里是因为非filter的查询一般会进行doc评分，所以速度回比较慢，而一旦指定了filter查询，则进行精确查询。

例1， 使用term，将默认进行文档评分
```sql
{
    "query"：{
        "term": {"price":20}
    }
}
```

例2，使用constant_score，取消文档评分

```sql
{
    "query"：{
        "constant_score":{
            "filter": {
                "term": {"price":20}
            }
        }
        
    }
}
```

例3， 但当使用constan_score：filter: term查询文本时，如果文本字段是analyzed时，使用term会抹去大小写，符号等，从而无法实现精确值查询，为避免该问题，需要将字段index设置为not_analyzed.
