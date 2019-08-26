# Index API

- [Index API](#index-api)
  - [创建索引](#%e5%88%9b%e5%bb%ba%e7%b4%a2%e5%bc%95)
    - [索引名限制](#%e7%b4%a2%e5%bc%95%e5%90%8d%e9%99%90%e5%88%b6)
    - [索引设置](#%e7%b4%a2%e5%bc%95%e8%ae%be%e7%bd%ae)
    - [Mappings](#mappings)
    - [Aliases](#aliases)
    - [Wait For Active Shards](#wait-for-active-shards)
    - [Skipping types](#skipping-types)
  - [删除索引](#%e5%88%a0%e9%99%a4%e7%b4%a2%e5%bc%95)

## 创建索引

基本命令如下:

```json
PUT test
```

### 索引名限制

- 仅限`小写`
- 不能包含`\, /, *, ?, ", <, >, |, 空格, 逗号, #`
- `7.0`之前的索引`可能包含冒号（:)`，但已被弃用，`7.0+不支持`
- 不能以 `- ，_，+` 开头
- 不能是 `.` 或 `..`
- 不能超过`255个字节`（注意它是`字节`，因此多字节字符将更快地达到 255 个限制）

### 索引设置

创建的每个索引都可以具有与其关联的特定设置，在 body 中定义：

```json
PUT test
{
    "settings" : {
        "index" : {
            "number_of_shards" : 3,
            "number_of_replicas" : 2
        }
    }
}

# 或更简洁
PUT test
{
    "settings" : {
        "number_of_shards" : 3,
        "number_of_replicas" : 2
    }
}
```

- `number_of_shards`的默认值是 5
- `number_of_replocas`的默认值是 1(每个主分片的一个副本)

### Mappings

API 允许提供类型映射

```json
PUT test
{
    "settings" : {
        "number_of_shards" : 1
    },
    "mappings" : {
        "_doc" : {
            "properties" : {
                "field1" : { "type" : "text" }
            }
        }
    }
}
```

### Aliases

API 允许提供一组别名

```json
PUT test
{
    "aliases" : {
        "alias_1" : {},
        "alias_2" : {
            "filter" : {
                "term" : {"user" : "kimchy" }
            },
            "routing" : "kimchy"
        }
    }
}
```

### Wait For Active Shards

默认情况下，索引创建仅在每个分片的主副本已启动或请求超时时才返回对客户端的响应。索引创建响应将指示发生了什么：

```json
{
  "acknowledged": true,
  "shards_acknowledged": true,
  "index": "test"
}
```

- `acknowledged` 指示是否在集群中成功创建索引
- `shards_acknowledged` 指示在超时之前是否为索引中的每个分片启动了必要数量的分片副本

**注意：**即使`acknowledged`和`shards_acknowledged`为 false，但是索引依旧可能创建成功，这些值仅表示操作是否在超时前完成

- 如果`acknowledged`为 false，那么即使在使用新创建的索引更新集群状态超时，但之后可能很快就完成创建
- 如果`shards_acknowledged` 为 false，那么说明激活必要的分片超时，默认只启动主分片
- 我们可以通过修改索引设置中的`index.write.wait_for_active_shards`，来改变必须激活的分片的数量。这个设置也会影响索引后续写操作设置中的`wait_for_active_shards`:

```json
PUT test
{
    "settings": {
        "index.write.wait_for_active_shards": "2"
    }
}
# 或者
PUT test?wait_for_active_shards=2
```

[wait_for_active_shards的详细描述](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/docs-index_.html#index-wait-for-active-shards)

### Skipping types

正在从Elasticsearch中删除类型：在7.0中，默认情况下，映射元素将不再将类型名称作为顶级键。 您可以通过设置`include_type_name = false`并将映射直接放在索引创建调用中的`mappings`下来选择加入此行为，而不指定类型名称。

```json
PUT test?include_type_name=false
{
  "mappings": {
    "properties": {
      "foo": {
        "type": "keyword"
      }
    }
  }
}

# 指定type
PUT test1?include_type_name=true
{
  "mappings": {
    "type1": {
      "properties": {
        "foo": {
          "type": "keyword"
        }
      }
    }
  }
}

resp=>
{
  "test1" : {
    "aliases" : { },
    "mappings" : {
      "type1" : {
        "properties" : {
          "foo" : {
            "type" : "keyword"
          }
        }
      }
    },
    "settings" : {
      "index" : {
        "creation_date" : "1566833130529",
        "number_of_shards" : "5",
        "number_of_replicas" : "1",
        "uuid" : "eap26vigRsWjJcw1B-bS8w",
        "version" : {
          "created" : "6080299"
        },
        "provided_name" : "test1"
      }
    }
  }
}
```


## 删除索引
