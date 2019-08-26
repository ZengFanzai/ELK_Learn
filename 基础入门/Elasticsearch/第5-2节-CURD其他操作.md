# CURD 其他操作

- [CURD 其他操作](#curd-%e5%85%b6%e4%bb%96%e6%93%8d%e4%bd%9c)
  - [Delete-By-Query-API](#delete-by-query-api)
    - [基本操作](#%e5%9f%ba%e6%9c%ac%e6%93%8d%e4%bd%9c)
    - [URL 参数](#url-%e5%8f%82%e6%95%b0)
    - [Response body](#response-body)
    - [其他选项](#%e5%85%b6%e4%bb%96%e9%80%89%e9%a1%b9)
    - [切片](#%e5%88%87%e7%89%87)
      - [手动切片](#%e6%89%8b%e5%8a%a8%e5%88%87%e7%89%87)
      - [自动分片](#%e8%87%aa%e5%8a%a8%e5%88%86%e7%89%87)
  - [Update-By-Query-API](#update-by-query-api)
  - [Reindex](#reindex)
    - [version_type](#versiontype)
    - [op_type](#optype)
    - [conflicts](#conflicts)
    - [添加 query](#%e6%b7%bb%e5%8a%a0-query)
    - [size](#size)
    - [\_source 提取部分字段](#source-%e6%8f%90%e5%8f%96%e9%83%a8%e5%88%86%e5%ad%97%e6%ae%b5)
    - [使用`script`进行 reindex](#%e4%bd%bf%e7%94%a8script%e8%bf%9b%e8%a1%8c-reindex)
    - [更多](#%e6%9b%b4%e5%a4%9a)

## Delete-By-Query-API

### 基本操作

最简单的\_delete_by_query 用法只会对匹配查询的每个文档执行删除操作。删除操作不会回滚，只会终止。如果你想对版本冲突计数而不是让它们中止，那么在请求 url 中设置`conflicts=proceed`在 body 中设置或`"conflicts": "proceed"`。

```json
POST twitter/_delete_by_query
{
  "query": {
    "match": {
      "message": "some message"
    }
  }
}

# 多index多type删除
POST twitter,blog/_docs,post/_delete_by_query
{
  "query": {
    "match_all": {}
  }
}

# conflicts=proceed
POST twitter/_doc/_delete_by_query?conflicts=proceed
{
  "query": {
    "match_all": {}
  }
}

# routing
POST twitter/_delete_by_query?routing=1
{
  "query": {
    "range" : {
        "age" : {
           "gte" : 10
        }
    }
  }
}

# 设置批量操作的size
POST twitter/_delete_by_query?scroll_size=5000
{
  "query": {
    "term": {
      "user": "kimchy"
    }
  }
}
```

### URL 参数

除了像 `pretty` 这样的标准参数之外，Reindex API 还支持 `refresh`，`wait_for_completion`，`wait_for_active_shards`，`timeout`，`scroll` 和 `requests_per_second`。

### Response body

```json
{
  "took": 147,
  "timed_out": false,
  "total": 119,
  "deleted": 119,
  "batches": 1,
  "version_conflicts": 0,
  "noops": 0,
  "retries": {
    "bulk": 0,
    "search": 0
  },
  "throttled_millis": 0,
  "requests_per_second": -1.0,
  "throttled_until_millis": 0,
  "failures": []
}
```

- `took` 从整个操作的开始到结束的毫秒数
- `time_out` 如果在查询执行删除期间执行的任何请求超时，则此标志设置为 true
- `total`已成功处理的文档数。
- `deleted` 已成功删除的文档数。
- `batches` 通过查询删除回滚的滚动响应数。
- `version_conflicts` 按查询删除的版本冲突数。
- `noops` 对于按查询删除，此字段始终等于零。它只存在于按查询删除，按查询更新和重新索引 API 返回具有相同结构的响应中。
- `retries` 通过查询删除尝试的重试次数。 bulk 是重试的批量操作数，search 是重试的搜索操作数。
- `throttled_millis` 请求休眠以符合`requests_per_second`的毫秒数
- `requests_per_second` 在通过查询删除期间有效执行的每秒请求数。
- `throttled_until_millis` 在`_delete_by_query`响应中，此字段应始终等于零。 它只在使用 Task API 时有意义，它表示下一次（自纪元以来的毫秒数），为了符合`requests_per_second`，将再次执行受限制的请求
- `failures` 如果在此过程中存在任何不可恢复的错误，则会出现故障数组。 如果这不是空的，那么请求因为那些失败而中止。 使用批处理实现查询删除，任何故障都会导致整个进程中止，但当前批处理中的所有故障都将收集到阵列中。 您可以使用`conflicts`选项来防止 reindex 在版本冲突中中止。

### 其他选项

- Works with the Task API
  - 您可以使用 Task API 获取所有正在运行的查询删除请求的状态, `GET _tasks?detailed=true&actions=*/delete/byquery`
- Works with the Cancel Task API
  - 可以使用 Task Cancel API 取消任何查询删除, `POST _tasks/r1A2WoRbTwKZ516z6NEs5A:36619/_cancel`
- Rethrottling
  - 可以使用\_rethrottle API 在正在运行的 reindex 上更改 requests_per_second 的值, `POST _reindex/r1A2WoRbTwKZ516z6NEs5A:36619/_rethrottle?requests_per_second=-1`

### 切片

按查询删除支持切片滚动以并行化删除过程。这种并行化可以提高效率，并提供一种方便的方法将请求分解为更小的部分。

#### 手动切片

通过为每个请求提供切片 ID 和切片总数，手动切片查询删除

```json
POST twitter/_delete_by_query
{
  "slice": {
    "id": 0,
    "max": 2
  },
  "query": {
    "range": {
      "likes": {
        "lt": 10
      }
    }
  }
}
POST twitter/_delete_by_query
{
  "slice": {
    "id": 1,
    "max": 2
  },
  "query": {
    "range": {
      "likes": {
        "lt": 10
      }
    }
  }
}
```

#### 自动分片

您也可以让查询删除自动并行使用切片滚动切片到`_uid`。使用`slices`指定要使用的切片数：

```json
POST twitter/_delete_by_query?refresh&slices=5
{
  "query": {
    "range": {
      "likes": {
        "lt": 10
      }
    }
  }
}
```

[参考官方链接](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/docs-delete-by-query.html)

## Update-By-Query-API

## Reindex

**注意：**

- `Reindex` 要求为源索引中的所有文档启用`_source`
- `Reindex`不会尝试设置目标索引。它不会复制源索引的设置。您应该在运行`_reindex`操作之前设置目标索引，包括设置映射，分片计数，副本等

```json
POST _reindex
{
  "source": {
    "index": "test"
  },
  "dest": {
    "index": "test2"
  }
}

resp=>
{
  "took" : 177,
  "timed_out" : false,
  "total" : 1,
  "updated" : 0,
  "created" : 1,
  "deleted" : 0,
  "batches" : 1,
  "version_conflicts" : 0,
  "noops" : 0,
  "retries" : {
    "bulk" : 0,
    "search" : 0
  },
  "throttled_millis" : 0,
  "requests_per_second" : -1.0,
  "throttled_until_millis" : 0,
  "failures" : [ ]
}

get=>
{
  "_index" : "test2",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1,
  "_seq_no" : 0,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "counter" : 1
  }
}

```

### version_type

如果遗漏`version_type`或将其设置为`internal`将导致 Elasticsearch 盲目地将文档转储到目标中，覆盖任何碰巧具有相同类型和 id 的文件

```json
POST _reindex
{
  "source": {
    "index": "test"
  },
  "dest": {
    "index": "test2",
    "version_type": "internal"
  }
}
```

将`version_type`设置为`external`将导致 Elasticsearch 保留源中的`version`，创建缺少的任何文档，并更新目标索引中具有旧版本的文档而不是源索引中的任何文档

```json
POST _reindex
{
  "source": {
    "index": "twitter"
  },
  "dest": {
    "index": "new_twitter",
    "version_type": "external"
  }
}
```

### op_type

设置`"op_type": create`将导致\_reindex 仅在目标索引中创建缺少的文档。所有现有文档都会导致版本冲突：

```json
POST _reindex
{
  "source": {
    "index": "twitter"
  },
  "dest": {
    "index": "new_twitter",
    "op_type": "create"
  }
}
```

### conflicts

默认情况下，版本冲突会终止`_reindex`进程。"conflict"请求 body 参数可用于指示`_reindex`继续处理版本冲突的下一个文档。请务必注意，其他错误类型的处理不受“conflict”参数的影响。 当设置`"conflicts": "proceed"`时，`_reindex`进程将继续发生版本冲突并返回遇到的版本冲突计数：

```json
POST _reindex
{
  "conflicts": "proceed",
  "source": {
    "index": "twitter"
  },
  "dest": {
    "index": "new_twitter",
    "op_type": "create"
  }
}
```

### 添加 query

通过向`source`添加类型或添加查询来限制文档。下边的例子只会将 kimchy 制作的推文复制到 new_twitter 中：

```json
POST _reindex
{
  "source": {
    "index": "twitter",
    "type": "_doc",
    "query": {
      "term": {
        "user": "kimchy"
      }
    }
  },
  "dest": {
    "index": "new_twitter"
  }
}
```

`source`中的`type`和`index`都可以是 list，允许您在一个请求中从许多源复制。如下请求将从 twitter 和 blog 索引中复制\_doc 和 post 类型的文档：

```json
POST _reindex
{
  "source": {
    "index": ["twitter", "blog"],
    "type": ["_doc", "post"]
  },
  "dest": {
    "index": "all_together",
    "type": "_doc"
  }
}
```

### size

通过设置`size`来限制处理文档的数量。这只会将一个文档从 twitter 复制到 new_twitter

```json
POST _reindex
{
  "size": 1,
  "source": {
    "index": "twitter"
  },
  "dest": {
    "index": "new_twitter"
  }
}
```

`sort`进行特定排序获取

```json
POST _reindex
{
  "size": 10000,
  "source": {
    "index": "twitter",
    "sort": { "date": "desc" }
  },
  "dest": {
    "index": "new_twitter"
  }
}
```

### \_source 提取部分字段

```json
POST _reindex
{
  "source": {
    "index": "twitter",
    "_source": ["user", "_doc"]
  },
  "dest": {
    "index": "new_twitter"
  }
}
```

### 使用`script`进行 reindex

与`_update_by_query`一样，`_reindex`支持修改文档的`script`。与`_update_by_query`不同，允许`script`修改文档的`metadata`：

```json
POST _reindex
{
  "source": {
    "index": "twitter"
  },
  "dest": {
    "index": "new_twitter",
    "version_type": "external"
  },
  "script": {
    "source": "if (ctx._source.foo == 'bar') {ctx._version++; ctx._source.remove('foo')}",
    "lang": "painless"
  }
}
```

就像在`_update_by_query`中一样，您可以设置`ctx.op`来更改在目标索引上执行的操作

- noop 如果脚本确定文档不必在目标索引中编入索引，请设置 ctx.op =“noop”。这种操作将在响应的 noop 计数器中显示统计
- delete 如果脚本确定必须从目标索引中删除文档，请设置 ctx.op =“delete”。删除将在响应中的`delete`计数器中报告。

### 更多

- Reindex from Remote
- 配置 SSL 参数
- URL 参数
  - 除了像 pretty 这样的标准参数之外，Reindex API 还支持 refresh，wait_for_completion，wait_for_active_shards，timeout，scroll 和 requests_per_second。
- Works with the Task API
  - 您可以使用 Task API 获取所有正在运行的 reindex 请求的状态, `GET _tasks?detailed=true&actions=*reindex`
- Works with the Cancel Task API
  - 可以使用 Task Cancel API 取消任何 reindex, `POST _tasks/r1A2WoRbTwKZ516z6NEs5A:36619/_cancel`
- Rethrottling
  - 可以使用\_rethrottle API 在正在运行的 reindex 上更改 requests_per_second 的值, `POST _reindex/r1A2WoRbTwKZ516z6NEs5A:36619/_rethrottle?requests_per_second=-1`
- Reindex 来改变字段名
  - \_reindex 可用于构建具有重命名字段的索引副本。

```json
# 假设您创建一个包含如下所示文档的索引：
POST test/_doc/1?refresh
{
  "text": "words words",
  "flag": "foo"
}

# 使用tag去替换flag
POST _reindex
{
  "source": {
    "index": "test"
  },
  "dest": {
    "index": "test2"
  },
  "script": {
    "source": "ctx._source.tag = ctx._source.remove(\"flag\")"
  }
}
```

- Reindex 支持切片滚动以并行化重建索引过程。这种并行化可以提高效率，并提供一种方便的方法将请求分解为更小的部分

[参考官方链接](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/docs-reindex.html)
