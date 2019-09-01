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
  - [获取索引](#%e8%8e%b7%e5%8f%96%e7%b4%a2%e5%bc%95)
    - [跳过types](#%e8%b7%b3%e8%bf%87types)
  - [判断索引是否存在](#%e5%88%a4%e6%96%ad%e7%b4%a2%e5%bc%95%e6%98%af%e5%90%a6%e5%ad%98%e5%9c%a8)
  - [开/关索引](#%e5%bc%80%e5%85%b3%e7%b4%a2%e5%bc%95)
    - [等待活动的分片](#%e7%ad%89%e5%be%85%e6%b4%bb%e5%8a%a8%e7%9a%84%e5%88%86%e7%89%87)
  - [收缩索引](#%e6%94%b6%e7%bc%a9%e7%b4%a2%e5%bc%95)
    - [准备一个索引用于收缩](#%e5%87%86%e5%a4%87%e4%b8%80%e4%b8%aa%e7%b4%a2%e5%bc%95%e7%94%a8%e4%ba%8e%e6%94%b6%e7%bc%a9)
    - [收缩一个索引](#%e6%94%b6%e7%bc%a9%e4%b8%80%e4%b8%aa%e7%b4%a2%e5%bc%95)
    - [注意](#%e6%b3%a8%e6%84%8f)
    - [监控收缩处理](#%e7%9b%91%e6%8e%a7%e6%94%b6%e7%bc%a9%e5%a4%84%e7%90%86)
    - [等待活动分片](#%e7%ad%89%e5%be%85%e6%b4%bb%e5%8a%a8%e5%88%86%e7%89%87)

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

[wait_for_active_shards 的详细描述](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/docs-index_.html#index-wait-for-active-shards)

### Skipping types

正在从 Elasticsearch 中删除类型：在 7.0 中，默认情况下，映射元素将不再将类型名称作为顶级键。 您可以通过设置`include_type_name = false`并将映射直接放在索引创建调用中的`mappings`下来选择加入此行为，而不指定类型名称。

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

可以使用通配符(\*)和逗号分隔符(,), 且别名(aliases)不能用于删除索引，删除所有索引(be careful!)用`_all`或`*`。

为了禁止允许`通过通配符(*)`或`_all`删除索引为了禁用，将`config`中的`action.destructive_requires_name`设置为`true`。也可以通过`集群更新设置api`更改此设置。

```json
DELETE /twitter
```

## 获取索引

get index API允许检索有关一个或多个索引的信息，也可以通过使用`_all`或`*`作为索引应用于多个索引或所有索引，可以用`逗号分隔符(*)`。

```json
GET /twitter
GET test1,test2
GET test*
```

### 跳过types

在7.0中已经删除`Types`。默认情况下，`mapping`元素将不再将类型名称作为顶级键返回。您可以通过在请求上设置include_type_name = false来选择加入此行为。

```json
GET test1?include_type_name=false
resp=>
{
  "test1" : {
    "aliases" : { },
    "mappings" : {
      "properties" : {
        "foo" : {
          "type" : "keyword"
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

# 多了type -> haha
GET test1?include_type_name=true
resp=>
{
  "test1" : {
    "aliases" : { },
    "mappings" : {
      "haha" : {
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

## 判断索引是否存在

404表示它不存在，200表示它存在。
该请求不区分索引和别名，即如果存在具有该名称的别名，则还返回状态代码200。

```json
HEAD twitter
resp=>
200 - OK
```

## 开/关索引

请求格式：/{index}/_close 和 /{index}/_open。

可以打开和关闭多个索引。如果请求显式引用缺少的索引，则会引发错误。可以使用`ignore_unavailable = true`参数禁用此行为。支持`通配符`和`_all`，`action.destructive_requires_name=true`来禁用此项

`close`索引占用大量磁盘空间，这可能会导致托管环境出现问题。通过将`cluster.indices.close.enable`设置为`false`，可以通过集群设置API禁用`close`索引。默认值为`true`。

```json
POST /my_index/_close
POST /my_index/_open
```

### 等待活动的分片

因为打开索引分配了它的分片，所以在创建索引时设置wait_for_active_shards也适用于索引打开操作。open index API上的wait_for_active_shards设置的默认值是0，这意味着命令不会等待分配shards。

## 收缩索引

收缩索引(shrink index)API 允许将现有索引缩减为具有较少主分片的新索引，目标索引中请求的主分片数必须是源索引中分片数的一个因子。 例如，具有8个主分片的索引可以缩小为4个，2个或1个主分片，或者具有15个主分片的索引可以缩小为5个，3个或1个。如果索引中的分片数是素数，它可以只能缩小为一个主要分片。 在收缩之前，索引中每个分片的（主要或副本）副本必须存在于同一节点上。

工作原理如下：

- 首先，创建一个新的目标索引，其定义与源索引相同，但使用较少数量的主分片
- 它将源索引中的片段硬链接到目标索引。(如果文件系统不支持硬链接，则会将所有段复制到新索引中，这是一个更耗时的过程。此外，如果使用多个数据路径，则不同数据路径上的分片如果不在同一磁盘上则需要分段文件的完整副本，因为硬链接不能跨磁盘工作)
- 最后，它恢复了目标索引，就好像是重新启动的关闭索引

### 准备一个索引用于收缩

```json
PUT /my_source_index/_settings
{
  "settings": {
    "index.routing.allocation.require._name": "shrink_node_name",
    "index.blocks.write": true
  }
}
```

- 强制将每个分片的副本重定向到名为`shink_node_name`的节点，有关更多选项参考**[Shard Allocation Filtering](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/shard-allocation-filtering.html)**
- 阻止对此索引的写入操作，同时仍允许更改元数据，如删除索引

重新迁移源索引可以需要一段时间。可以使用`_cat recovery` API，或者可以使用`cluster health` API通过`wait_for_no_relocating_shards`参数等待所有分片都迁移成功

### 收缩一个索引

目标：将`my_source_index`收缩为`my_target_index`的新索引

```json
POST my_source_index/_shrink/my_target_index?copy_settings=true
{
  "settings": {
    "index.routing.allocation.require._name": null,
    "index.blocks.write": null
  }
}
```

- 清除从源索引复制过来的`index.routing.allocation.require._name`配置
- 清除从源索引复制过来的`index.blocks.write`配置

一旦将目标索引添加到集群状态，上述请求就会立即返回——它不会等待收缩操作开始

### 注意

索引只有在满足以下条件才能进行收缩

- 目标索引不得存在
- 索引必须具有比目标索引更多的主分片
- 目标索引中的主分片数必须是源索引中主分片数的一个因子(两数相乘)
- 索引在所有分片上的的文档总数不能超过2,147,483,519个，因为当缩小为目标索引的单个分片时，这是可放入单个分片的最大文档数
- 处理收缩过程的节点必须具有足够的可用磁盘空间，以容纳现有索引的第二个副本

`_shrink`API和`create index`API相似并且接受`settings`和`aliases`参数

```json
POST my_source_index/_shrink/my_target_index?copy_settings=true
{
  "settings": {
    "index.number_of_replicas": 1,
    "index.number_of_shards": 1,
    "index.codec": "best_compression"
  },
  "aliases": {
    "my_search_indices": {}
  }
}
```

- `index.number_of_shards`表示目标索引的分片数。这必须是源索引分片数的一个因子
- `index.codec: best_compression`：最佳压缩仅在对索引进行新写入时生效，例如当[force_meging(强制合并)](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/indices-forcemerge.html)分片到单个片段

**注意：**默认情况下，除index.analysis，index.similarity和index.sort设置外，源索引上的索引设置不会在收缩操作期间复制。 除了不可复制的设置之外，可以通过将URL参数`copy_settings = true`添加到请求来将源索引中的设置复制到目标索引。 请注意，`copy_settings`不能设置为`false`。 参数copy_settings将在8.0.0中删除

```json
POST my_source_index/_shrink/my_target_index?copy_settings=false
{
  "settings": {
    "index.routing.allocation.require._name": null,
    "index.blocks.write": null
  }
}

resp=>
{
  "error": {
    "root_cause": [
      {
        "type": "illegal_argument_exception",
        "reason": "parameter [copy_settings] can not be explicitly set to [false]"
      }
    ],
    "type": "illegal_argument_exception",
    "reason": "parameter [copy_settings] can not be explicitly set to [false]"
  },
  "status": 400
}
```

### 监控收缩处理

可以使用`_cat recovery`API监视收缩过程，或者可以使用`cluster health`API等待，直到通过将`wait_for_status`参数设置为黄色来分配所有主分片。

在分配任何分片之前，只要目标索引已添加到群集状态，`_shrink`API就会返回。 此时，所有分片都处于未分配的状态。 如果由于任何原因无法在收缩节点上分配目标索引，则其主分片将保持未分配状态，直到可以在该节点上分配为止。

一旦分配了主分片，它就会转到状态`初始化`，并开始缩小过程。 收缩操作完成后，分片将变为`活动`状态。 此时，Elasticsearch将尝试分配任何副本，并可能决定将主分片重定位到另一个节点。

### 等待活动分片

由于收缩操作会创建一个新的索引来缩小分片，因此在索引创建时[wait for active shards](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/indices-create-index.html#create-index-wait-for-active-shards)设置也适用于缩减索引操作。
