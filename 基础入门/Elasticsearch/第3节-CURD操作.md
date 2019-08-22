# CURD 操作

- [CURD 操作](#curd-%e6%93%8d%e4%bd%9c)
  - [Index API](#index-api)
    - [通过ID为document编制索引](#%e9%80%9a%e8%bf%87id%e4%b8%badocument%e7%bc%96%e5%88%b6%e7%b4%a2%e5%bc%95)
    - [不提供ID为document编制索引](#%e4%b8%8d%e6%8f%90%e4%be%9bid%e4%b8%badocument%e7%bc%96%e5%88%b6%e7%b4%a2%e5%bc%95)
    - [自动创建索引](#%e8%87%aa%e5%8a%a8%e5%88%9b%e5%bb%ba%e7%b4%a2%e5%bc%95)
    - [versioning](#versioning)
  - [Get API](#get-api)
  - [Update API](#update-api)
  - [Delete API](#delete-api)

## Index API

在 es 术语中，在 es 的 index 中添加(或创建)document 到一个 type 称为`索引操作(indexing operation)`。本质上来说，它通过分析document的所有字段并构建反转索引将文档添加到索引中。这就是为什么称此操作为`索引操作`的原因。

有两种方法添加document

- 通过ID为document编制索引
- 不提供ID为document编制索引

### 通过ID为document编制索引

请求的格式: `PUT /<index>/<type>/<id>` + `JSON body`

```text
PUT /catalog/product/1
{
  "sku": "SP000001",
  "title": "Elasticsearch for Hadoop",
  "description": "Elasticsearch for Hadoop",
  "author": "Vishal Shukla",
  "ISBN": "1785288997",
  "price": 26.99
}

=> POST也可以使用
POST /catalog/_doc/11
{
  "sku": "SP000004",
  "title": "Mastering Elasticsearch",
  "description": "Mastering Elasticsearch",
  "author": "Bharvi Dixit",
  "price": 54.99
}

响应=>
{
  "_index" : "catalog",
  "_type" : "_doc",
  "_id" : "11",
  "_version" : 18,
  "result" : "updated",
  "_shards" : {
    "total" : 3,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 20,
  "_primary_term" : 5
}


GET /catalog/_doc/11

响应=>
{
  "_index" : "catalog",
  "_type" : "_doc",
  "_id" : "11",
  "_version" : 17,
  "_seq_no" : 19,
  "_primary_term" : 5,
  "found" : true,
  "_source" : {
    "sku" : "SP000004",
    "title" : "Mastering Elasticsearch",
    "description" : "Mastering Elasticsearch",
    "author" : "Bharvi Dixit",
    "price" : 54.99
  }
}

```

`_shards`标头提供有关`索引操作`的`复制过程`的信息。

- total - 指示应在其上执行`索引操作`的分片副本（主分片和副本分片）的数量。

- successful- 指示`索引操作`成功的分片副本数。
- failed - 在副本分片上`索引操作`失败的情况下包含复制相关错误的数组。

在`successful`至少为1的情况下，`索引操作`是成功的

### 不提供ID为document编制索引

请求格式: `POST /<index>/<type>` + `JSON body`

```text
POST /catalog/_doc
{
  "sku": "SP000003",
  "title": "Mastering Elasticsearch",
  "description": "Mastering Elasticsearch",
  "author": "Bharvi Dixit",
  "price": 54.99
}

=> 响应如下
{
  "_index" : "catalog",
  "_type" : "_doc",
  "_id" : "OYTduWwBhIVD-G8qdZOQ",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 3,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 5
}

=> "_id" : "OYTduWwBhIVD-G8qdZOQ" 自动生成一个id
```

### 自动创建索引

自动索引创建由`action.auto_create_index`设置控制。此设置`默认`为`true`，表示始终自动创建索引。

```text
# 检测是否存在索引
HEAD /catalog2
响应=>
404 - Not Found

# 添加doc
PUT /catalog2/_doc/1
{
  "sku": "SP000012",
  "title": "自动创建索引",
  "description": "自动创建索引"
}

响应=>
{
  "_index" : "catalog2",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}

GET /catalog2/_doc/1
响应=>
{
  "_index" : "catalog2",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1,
  "_seq_no" : 0,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "sku" : "SP000012",
    "title" : "自动创建索引",
    "description" : "自动创建索引"
  }
}

# 查看索引的配置
GET /catalog2/_settings
=>响应
{
  "catalog2" : {
    "settings" : {
      "index" : {
        "creation_date" : "1566490527004",
        "number_of_shards" : "1",
        "number_of_replicas" : "1",
        "uuid" : "cDhV48rZT-as6VVRD4TVQg",
        "version" : {
          "created" : "7020099"
        },
        "provided_name" : "catalog2"
      }
    }
  }
}

# 查看mapping
GET /catalog2/_mapping
响应=>
{
  "catalog2" : {
    "mappings" : {
      "properties" : {
        "description" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "sku" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "title" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        }
      }
    }
  }
}
```

### versioning

每个索引文档都有一个版本号。

## Get API

请求格式: `GET /<index>/<type>/<id>`

```text
GET /catalog/_doc/12
{
  "_index" : "catalog",
  "_type" : "_doc",
  "_id" : "12",
  "_version" : 1,
  "_seq_no" : 0,
  "_primary_term" : 5,
  "found" : true,
  "_source" : {
    "sku" : "SP000012",
    "title" : "Test Elasticsearch",
    "description" : "Test Elasticsearch",
    "author" : "xxxxxx",
    "price" : 54.99
  }
}

# 检测文档是否存在
HEAD /catalog/_doc/12

=>响应
200 - OK
```

## Update API

## Delete API
