# CURD 操作

- [CURD 操作](#curd-%e6%93%8d%e4%bd%9c)
  - [Index API](#index-api)
    - [通过 ID 为 document 编制索引](#%e9%80%9a%e8%bf%87-id-%e4%b8%ba-document-%e7%bc%96%e5%88%b6%e7%b4%a2%e5%bc%95)
    - [不提供 ID 为 document 编制索引](#%e4%b8%8d%e6%8f%90%e4%be%9b-id-%e4%b8%ba-document-%e7%bc%96%e5%88%b6%e7%b4%a2%e5%bc%95)
    - [自动创建索引](#%e8%87%aa%e5%8a%a8%e5%88%9b%e5%bb%ba%e7%b4%a2%e5%bc%95)
    - [versioning](#versioning)
      - [Version types [internal,external]](#version-types-internalexternal)
      - [Operation Type](#operation-type)
      - [Routing](#routing)
      - [Timeout](#timeout)
  - [Get API](#get-api)
    - [Source filtering](#source-filtering)
    - [Stored Fields](#stored-fields)
    - [直接使用\_source](#%e7%9b%b4%e6%8e%a5%e4%bd%bf%e7%94%a8source)
  - [Update API](#update-api)
    - [使用 script 进行 update](#%e4%bd%bf%e7%94%a8-script-%e8%bf%9b%e8%a1%8c-update)
    - [通过部分 doc 进行 update, 如果已经存在，这忽略 update 请求,可以通过设置`"detect_noop": false`](#%e9%80%9a%e8%bf%87%e9%83%a8%e5%88%86-doc-%e8%bf%9b%e8%a1%8c-update-%e5%a6%82%e6%9e%9c%e5%b7%b2%e7%bb%8f%e5%ad%98%e5%9c%a8%e8%bf%99%e5%bf%bd%e7%95%a5-update-%e8%af%b7%e6%b1%82%e5%8f%af%e4%bb%a5%e9%80%9a%e8%bf%87%e8%ae%be%e7%bd%ae%22detectnoop%22-false)
    - [Upserts](#upserts)
      - [scripted_upsert](#scriptedupsert)
      - [doc_as_upsert](#docasupsert)
    - [参数](#%e5%8f%82%e6%95%b0)
      - [`if_seq_no` 和 `if_primary_term`](#ifseqno-%e5%92%8c-ifprimaryterm)
  - [Delete API](#delete-api)
    - [乐观锁的并发控制](#%e4%b9%90%e8%a7%82%e9%94%81%e7%9a%84%e5%b9%b6%e5%8f%91%e6%8e%a7%e5%88%b6)
    - [使用`routing`指定删除](#%e4%bd%bf%e7%94%a8routing%e6%8c%87%e5%ae%9a%e5%88%a0%e9%99%a4)
    - [其他选项](#%e5%85%b6%e4%bb%96%e9%80%89%e9%a1%b9)

## Index API

在 es 术语中，在 es 的 index 中添加(或创建)document 到一个 type 称为`索引操作(indexing operation)`。本质上来说，它通过分析 document 的所有字段并构建反转索引将文档添加到索引中。这就是为什么称此操作为`索引操作`的原因。

有两种方法添加 document

- 通过 ID 为 document 编制索引
- 不提供 ID 为 document 编制索引

### 通过 ID 为 document 编制索引

请求的格式: `PUT /<index>/<type>/<id>` + `JSON body`

```json
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

resp=>
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

resp=>
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

在`successful`至少为 1 的情况下，`索引操作`是成功的

### 不提供 ID 为 document 编制索引

请求格式: `POST /<index>/<type>` + `JSON body`

```json
POST /catalog/_doc
{
  "sku": "SP000003",
  "title": "Mastering Elasticsearch",
  "description": "Mastering Elasticsearch",
  "author": "Bharvi Dixit",
  "price": 54.99
}

=> resp如下
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

```json
# 检测是否存在索引
HEAD /catalog2
resp=>
404 - Not Found

# 添加doc
PUT /catalog2/_doc/1
{
  "sku": "SP000012",
  "title": "自动创建索引",
  "description": "自动创建索引"
}

resp=>
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
resp=>
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
=>resp
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
resp=>
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

es 中每个文档都有对应的版本信息，可以使用 version 版本参数用来实现并发情况下的乐观锁机制

```json
# es 6.8
PUT /catalog2/_doc/1?version=2
{
  "sku": "SP00002",
  "title": "自动创建索引2",
  "description": "自动创建索引2"
}

resp, 正常=>
{
  "_index" : "catalog2",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 3,
  "result" : "updated",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 2,
  "_primary_term" : 1
}

resp,version版本低时=>
{
  "error": {
    "root_cause": [
      {
        "type": "version_conflict_engine_exception",
        "reason": "[_doc][1]: version conflict, current version [3] is different than the one provided [2]",
        "index_uuid": "xE24mW98To6dI5UjG1NQQw",
        "shard": "3",
        "index": "catalog2"
      }
    ],
    "type": "version_conflict_engine_exception",
    "reason": "[_doc][1]: version conflict, current version [3] is different than the one provided [2]",
    "index_uuid": "xE24mW98To6dI5UjG1NQQw",
    "shard": "3",
    "index": "catalog2"
  },
  "status": 409
}

# es7.0，需要version_type=external
PUT /catalog/_doc/11?version=21&version_type=external
{
  "sku": "SP000021",
  "title": "修改version的数据21",
  "description": "修改version的数据21"
}

```

#### Version types [internal,external]

- `internal` 仅在给定版本和与存储文档的版本相同时，才索引文档
- `external`或`external_gt` 仅在给定版本高于存储版本，或当前文档不存在时，才索引文档
- `external_gte` 大于等于，或不存在，和上一个类似, 适用于特殊用例，应谨慎使用

```json
# external
PUT /catalog2/_doc/2?version=5&version_type=internal
{
  "sku": "SP00002",
  "title": "自动创建索引2",
  "description": "自动创建索引2"
}

resp, 会有一个警告=>
#! Deprecation: Usage of internal versioning for optimistic concurrency control is deprecated and will be removed. Please use the `if_seq_no` and `if_primary_term` parameters instead. (request for index [catalog2], type [_doc], id [2])
{
  "_index" : "catalog2",
  "_type" : "_doc",
  "_id" : "2",
  "_version" : 6,
  "result" : "updated",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 2,
  "_primary_term" : 1
}

# external或external_gt
PUT /catalog2/_doc/2?version=5&version_type=external
{
  "sku": "SP00002",
  "title": "自动创建索引2",
  "description": "自动创建索引2"
}

resp=>
{
  "_index" : "catalog2",
  "_type" : "_doc",
  "_id" : "2",
  "_version" : 5,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}

# external_gte
PUT /catalog2/_doc/2?version=5&version_type=external_gte
{
  "sku": "SP00002",
  "title": "自动创建索引2",
  "description": "自动创建索引2"
}

resp=>
{
  "_index" : "catalog2",
  "_type" : "_doc",
  "_id" : "2",
  "_version" : 5,
  "result" : "updated",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 1,
  "_primary_term" : 1
}

```

#### Operation Type

索引操作还接受可用于强制创建操作的 op_type, 使用 create 时，如果索引中已存在该 id 的文档，则索引操作将失败。

```json
PUT twitter/_doc/1?op_type=create
{
    "user" : "kimchy",
    "post_date" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}

# 或者
PUT twitter/_doc/1/_create
{
    "user" : "kimchy",
    "post_date" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}
```

#### Routing

默认情况下，同时使用文档的 id 值的 hash 或 routing 来控制。

```json
POST twitter/_doc?routing=kimchy
{
    "user" : "kimchy",
    "post_date" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}
```

#### Timeout

`timeout`参数可用于显式指定等待的时间。默认情况下，索引操作将在主分片上等待最多 1 分钟，然后失败并响应错误

```json
PUT twitter/_doc/1?timeout=5m
{
    "user" : "kimchy",
    "post_date" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}
```

## Get API

Get API 是实时的，并且不受索引刷新率的影响

请求格式: `GET /<index>/<type>/<id>`

```json
GET /catalog/_doc/12

resp=>
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

=>resp
200 - OK
```

### Source filtering

可以使用`_source`，`_source_include`，`_source_exclude`进行选择是否需要\_source 字段, 这后两个参数都使用`逗号分隔`的字段列表或`通配符表达式`

```json
GET /catalog2/_doc/2?_source=false

resp=>
{
  "_index" : "catalog2",
  "_type" : "_doc",
  "_id" : "2",
  "_version" : 6,
  "_seq_no" : 2,
  "_primary_term" : 1,
  "found" : true
}

GET /catalog2/_doc/2?_source_includes=sku,title

resp=>
{
  "_index" : "catalog2",
  "_type" : "_doc",
  "_id" : "2",
  "_version" : 6,
  "_seq_no" : 2,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "sku" : "SP00002",
    "title" : "自动创建索引2"
  }
}

GET /catalog2/_doc/2?_source_excludes=title

resp=>
{
  "_index" : "catalog2",
  "_type" : "_doc",
  "_id" : "2",
  "_version" : 6,
  "_seq_no" : 2,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "description" : "自动创建索引2",
    "sku" : "SP00002"
  }
}
```

### Stored Fields

```json

# 构建如下索引的mapping
PUT twitter
{
   "mappings": {
      "_doc": {
         "properties": {
            "counter": {
               "type": "integer",
               "store": false
            },
            "tags": {
               "type": "keyword",
               "store": true
            }
         }
      }
   }
}

# 添加一个doc
PUT twitter/_doc/1
{
    "counter" : 1,
    "tags" : ["red"]
}

# GET
GET twitter/_doc/1?stored_fields=tags,counter

=>resp
{
   "_index": "twitter",
   "_type": "_doc",
   "_id": "1",
   "_version": 1,
   "_seq_no" : 22,
   "_primary_term" : 1,
   "found": true,
   "fields": {
      "tags": [
         "red"
      ]
   }
}
```

[参考文章“elasticsearch 的 store 属性跟\_source 字段”](https://blog.csdn.net/jingkyks/article/details/41785887)

### 直接使用\_source

```json
GET twitter/_doc/1/_source

resp=>
{
  "counter" : 1,
  "tags" : [
    "red"
  ]
}

```

## Update API

请求格式: `POST <index>/<type>/<id>/_update`

### 使用 script 进行 update

```json
# 新建doc
PUT test/_doc/1
{
    "counter" : 1,
    "tags" : ["red"]
}

# GET
GET test/_doc/1
resp=>
{
  "_index" : "test",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 6,
  "_seq_no" : 5,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "counter" : 1,
    "tags" : [
      "red"
    ]
  }
}

# update
POST test/_doc/1/_update
{
    "script" : {
        "source": "ctx._source.counter += params.count",
        "lang": "painless",
        "params" : {
            "count" : 4
        }
    }
}

resp=>
{
  "_index" : "test",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 7,
  "result" : "updated",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 6,
  "_primary_term" : 1
}

# GET New Value
GET test/_doc/1
resp=>
{
  "_index" : "test",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 7,
  "_seq_no" : 6,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "counter" : 5,
    "tags" : [
      "red"
    ]
  }
}

# tags添加一个tag
POST test/_doc/1/_update
{
    "script" : {
        "source": "ctx._source.tags.add(params.tag)",
        "lang": "painless",
        "params" : {
            "tag" : "blue"
        }
    }
}

get=>
{
  "_index" : "test",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 8,
  "_seq_no" : 7,
  "_primary_term" : 2,
  "found" : true,
  "_source" : {
    "counter" : 5,
    "tags" : [
      "red",
      "blue"
    ]
  }
}

# if条件
POST test/_doc/1/_update
{
    "script" : {
        "source": "if (ctx._source.tags.contains(params.tag)) { ctx._source.tags.remove(ctx._source.tags.indexOf(params.tag)) }",
        "lang": "painless",
        "params" : {
            "tag" : "blue"
        }
    }
}

get=>
{
  "_index" : "test",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 9,
  "_seq_no" : 8,
  "_primary_term" : 2,
  "found" : true,
  "_source" : {
    "counter" : 5,
    "tags" : [
      "red"
    ]
  }
}
```

### 通过部分 doc 进行 update, 如果已经存在，这忽略 update 请求,可以通过设置`"detect_noop": false`

```json
POST test/_doc/1/_update
{
    "doc" : {
        "name" : "new_name"
    }
}

get=>
{
  "_index" : "test",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 10,
  "_seq_no" : 9,
  "_primary_term" : 2,
  "found" : true,
  "_source" : {
    "counter" : 5,
    "tags" : [
      "red"
    ],
    "name" : "new_name"
  }
}

忽略update req=>
{
  "_index" : "test",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 10,
  "result" : "noop",
  "_shards" : {
    "total" : 0,
    "successful" : 0,
    "failed" : 0
  }
}

POST test/_doc/1/_update
{
    "doc" : {
        "name" : "new_name"
    },
    "detect_noop": false
}
```

### Upserts

```json

# upsert scrpit, 存在+4，不存在为1
POST test/_doc/1/_update
{
    "script" : {
        "source": "ctx._source.counter += params.count",
        "lang": "painless",
        "params" : {
            "count" : 4
        }
    },
    "upsert" : {
        "counter" : 1
    }
}
```

#### scripted_upsert

无论 doc 是否存在，都希望 script 运行，需要设置`scripted_upsert`为`true`，例如通过 script 初始化 doc，而不是 upsert 的元素

```json
# 该实例无法正常运行
POST sessions/session/dh3sgudg8gsrgl/_update
{
    "scripted_upsert":true,
    "script" : {
        "id": "my_web_session_summariser",
        "params" : {
            "pageViewEvent" : {
                "url":"foo.com/bar",
                "response":404,
                "time":"2014-01-01 12:32"
            }
        }
    },
    "upsert" : {}
}
```

#### doc_as_upsert

将`doc_as_upsert`设置为`true`将使用 doc 的内容作为 upsert 值，而不是发送部分 doc 加上 upsert 文档

```json
POST test/_doc/1/_update
{
    "doc" : {
        "name" : "new_name"
    },
    "doc_as_upsert" : true
}
```

### 参数

update 操作支持如下的查询参数

| 参数                   | 介绍                                                                                                                                                                                                                                                                                                                                                 |
| ---------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| retry_on_conflict      | 在更新的 get 和 indexing 阶段之间，另一个进程可能已经更新了同一文档，默认情况下，更新将因版本冲突异常而失败，`retry_on_conflict`参数控制在最终抛出异常之前重试更新的次数                                                                                                                                                                             |
| routing                | `routing`用于将更新请求路由到正确的分片，并在更新的文档不存在时为 upsert 请求设置路由。不能用于更新现有文档的路由。                                                                                                                                                                                                                                  |
| timeout                | 等待碎片变为可用的超时时间                                                                                                                                                                                                                                                                                                                           |
| wait_for_active_shards | 在继续更新操作之前需要处于活动状态的分片副本数，[详情](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/docs-index_.html#index-wait-for-active-shards)                                                                                                                                                                                    |
| refresh                | 控制何时此请求所做的更改对搜索可见, [refresh 参考](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/docs-refresh.html)                                                                                                                                                                                                                    |
| \_source               | 允许控制是否以及如何在响应中返回更新的源。默认情况下，不会返回更新的源，详情可见[Source filtering](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/search-request-source-filtering.html)                                                                                                                                                 |
| version                | 更新 API 在内部使用 Elasticsearch 版本控制支持以确保在更新期间文档不会更改。您可以使用 version 参数指定仅在文档的版本与指定版本匹配时才更新文档，在 es6.7.0 中弃用。请改用 if_seq_no 和 if_primary_term。有关更多详细信息，请参阅[乐观锁并发控制](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/optimistic-concurrency-control.html)。 |

**注意：**update API 不支持 External（version types `external`和`external_gte`）或 forced（version types `force`）版本控制，因为它会导致 Elasticsearch 版本号与外部系统不同步。

#### `if_seq_no` 和 `if_primary_term`

更新操作可以是有条件的，只有在对文档的最后一次修改分配了`if_seq_no`和`if_primary_term`时才会执行。如果检测到不匹配，则操作将导致 VersionConflictException 和 409 状态代码，请参阅[乐观锁并发控制](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/optimistic-concurrency-control.html)

## Delete API

```json
DELETE /test/_doc/1
resp=>
{
  "_index" : "test",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 23,
  "result" : "deleted",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 22,
  "_primary_term" : 3
}

```

### 乐观锁的并发控制

`delete`操作可以是有条件的，只有在为文档的最后一次修改分配了`if_seq_no`和`if_primary_term`参数指定的序列号和主要术语时才能执行。如果检测到不匹配，则操作将导致`VersionConflictException`和`409`状态码。

### 使用`routing`指定删除

```json
DELETE /test/_doc/1?routing=kimchy
{
  "_index" : "test",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 4,
  "result" : "not_found",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 5,
  "_primary_term" : 3
}
```

### 其他选项

- [自动创建索引](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/docs-delete.html#delete-index-creation)
- [Wait For Active Shards](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/docs-delete.html#delete-wait-for-active-shards)
- [Refresh](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/docs-delete.html#delete-refresh)
- [Timeout](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/docs-delete.html#delete-timeout)
