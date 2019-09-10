# Index API

- [Index API](#index-api)
  - [创建索引(Create Index)](#%e5%88%9b%e5%bb%ba%e7%b4%a2%e5%bc%95create-index)
    - [索引名限制](#%e7%b4%a2%e5%bc%95%e5%90%8d%e9%99%90%e5%88%b6)
    - [索引设置](#%e7%b4%a2%e5%bc%95%e8%ae%be%e7%bd%ae)
    - [Mappings](#mappings)
    - [Aliases](#aliases)
    - [Wait For Active Shards](#wait-for-active-shards)
    - [Skipping types](#skipping-types)
  - [删除索引(Delete Index)](#%e5%88%a0%e9%99%a4%e7%b4%a2%e5%bc%95delete-index)
  - [获取索引(Get Index)](#%e8%8e%b7%e5%8f%96%e7%b4%a2%e5%bc%95get-index)
    - [跳过 types](#%e8%b7%b3%e8%bf%87-types)
  - [判断索引是否存在(Indices Exists)](#%e5%88%a4%e6%96%ad%e7%b4%a2%e5%bc%95%e6%98%af%e5%90%a6%e5%ad%98%e5%9c%a8indices-exists)
  - [开/关索引(Open/Close Index API)](#%e5%bc%80%e5%85%b3%e7%b4%a2%e5%bc%95openclose-index-api)
    - [等待活动的分片](#%e7%ad%89%e5%be%85%e6%b4%bb%e5%8a%a8%e7%9a%84%e5%88%86%e7%89%87)
  - [收缩索引(Shrink Index)](#%e6%94%b6%e7%bc%a9%e7%b4%a2%e5%bc%95shrink-index)
    - [准备一个索引用于收缩](#%e5%87%86%e5%a4%87%e4%b8%80%e4%b8%aa%e7%b4%a2%e5%bc%95%e7%94%a8%e4%ba%8e%e6%94%b6%e7%bc%a9)
    - [收缩一个索引](#%e6%94%b6%e7%bc%a9%e4%b8%80%e4%b8%aa%e7%b4%a2%e5%bc%95)
    - [注意](#%e6%b3%a8%e6%84%8f)
    - [监控收缩处理](#%e7%9b%91%e6%8e%a7%e6%94%b6%e7%bc%a9%e5%a4%84%e7%90%86)
    - [等待活动分片](#%e7%ad%89%e5%be%85%e6%b4%bb%e5%8a%a8%e5%88%86%e7%89%87)
  - [拆分索引(Split Index)](#%e6%8b%86%e5%88%86%e7%b4%a2%e5%bc%95split-index)
    - [拆分索引的工作原理](#%e6%8b%86%e5%88%86%e7%b4%a2%e5%bc%95%e7%9a%84%e5%b7%a5%e4%bd%9c%e5%8e%9f%e7%90%86)
    - [为什么 ES 不支持增量重新分片](#%e4%b8%ba%e4%bb%80%e4%b9%88-es-%e4%b8%8d%e6%94%af%e6%8c%81%e5%a2%9e%e9%87%8f%e9%87%8d%e6%96%b0%e5%88%86%e7%89%87)
    - [拆分索引实例](#%e6%8b%86%e5%88%86%e7%b4%a2%e5%bc%95%e5%ae%9e%e4%be%8b)
      - [准备一个用于拆分的索引](#%e5%87%86%e5%a4%87%e4%b8%80%e4%b8%aa%e7%94%a8%e4%ba%8e%e6%8b%86%e5%88%86%e7%9a%84%e7%b4%a2%e5%bc%95)
      - [拆分该索引](#%e6%8b%86%e5%88%86%e8%af%a5%e7%b4%a2%e5%bc%95)
  - [翻转索引(Rollover Index)](#%e7%bf%bb%e8%bd%ac%e7%b4%a2%e5%bc%95rollover-index)
    - [命名新索引](#%e5%91%bd%e5%90%8d%e6%96%b0%e7%b4%a2%e5%bc%95)
    - [在翻转 API 中使用 Date Math](#%e5%9c%a8%e7%bf%bb%e8%bd%ac-api-%e4%b8%ad%e4%bd%bf%e7%94%a8-date-math)
    - [定义新索引](#%e5%ae%9a%e4%b9%89%e6%96%b0%e7%b4%a2%e5%bc%95)
    - [空运行(Dry Run)](#%e7%a9%ba%e8%bf%90%e8%a1%8cdry-run)
    - [Rollover API——等待活动分片](#rollover-api%e7%ad%89%e5%be%85%e6%b4%bb%e5%8a%a8%e5%88%86%e7%89%87)
    - [写索引别名行为(Write Index Alias Behavior)](#%e5%86%99%e7%b4%a2%e5%bc%95%e5%88%ab%e5%90%8d%e8%a1%8c%e4%b8%bawrite-index-alias-behavior)

## 创建索引(Create Index)

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

## 删除索引(Delete Index)

可以使用通配符(\*)和逗号分隔符(,), 且别名(aliases)不能用于删除索引，删除所有索引(be careful!)用`_all`或`*`。

为了禁止允许`通过通配符(*)`或`_all`删除索引为了禁用，将`config`中的`action.destructive_requires_name`设置为`true`。也可以通过`集群更新设置api`更改此设置。

```json
DELETE / twitter
```

## 获取索引(Get Index)

get index API 允许检索有关一个或多个索引的信息，也可以通过使用`_all`或`*`作为索引应用于多个索引或所有索引，可以用`逗号分隔符(*)`。

```json
GET /twitter
GET test1,test2
GET test*
```

### 跳过 types

在 7.0 中已经删除`Types`。默认情况下，`mapping`元素将不再将类型名称作为顶级键返回。您可以通过在请求上设置 include_type_name = false 来选择加入此行为。

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

## 判断索引是否存在(Indices Exists)

404 表示它不存在，200 表示它存在。
该请求不区分索引和别名，即如果存在具有该名称的别名，则还返回状态代码 200。

```json
HEAD twitter
resp=>
200 - OK
```

## 开/关索引(Open/Close Index API)

请求格式：/{index}/\_close 和 /{index}/\_open。

可以打开和关闭多个索引。如果请求显式引用缺少的索引，则会引发错误。可以使用`ignore_unavailable = true`参数禁用此行为。支持`通配符`和`_all`，`action.destructive_requires_name=true`来禁用此项

`close`索引占用大量磁盘空间，这可能会导致托管环境出现问题。通过将`cluster.indices.close.enable`设置为`false`，可以通过集群设置 API 禁用`close`索引。默认值为`true`。

```json
POST /my_index/_close
POST /my_index/_open
```

### 等待活动的分片

因为打开索引分配了它的分片，所以在创建索引时设置 wait_for_active_shards 也适用于索引打开操作。open index API 上的 wait_for_active_shards 设置的默认值是 0，这意味着命令不会等待分配 shards。

## 收缩索引(Shrink Index)

收缩索引(shrink index)API 允许将现有索引缩减为具有较少主分片的新索引，目标索引中请求的主分片数必须是源索引中分片数的一个因子。 例如，具有 8 个主分片的索引可以缩小为 4 个，2 个或 1 个主分片，或者具有 15 个主分片的索引可以缩小为 5 个，3 个或 1 个。如果索引中的分片数是素数，它可以只能缩小为一个主要分片。 在收缩之前，索引中每个分片的（主要或副本）副本必须存在于同一节点上。

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

重新迁移源索引可以需要一段时间。可以使用`_cat recovery` API，或者可以使用`cluster health` API 通过`wait_for_no_relocating_shards`参数等待所有分片都迁移成功

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
- 索引在所有分片上的的文档总数不能超过 2,147,483,519 个，因为当缩小为目标索引的单个分片时，这是可放入单个分片的最大文档数
- 处理收缩过程的节点必须具有足够的可用磁盘空间，以容纳现有索引的第二个副本

`_shrink`API 和`create index`API 相似并且接受`settings`和`aliases`参数

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

**注意：**默认情况下，除 index.analysis，index.similarity 和 index.sort 设置外，源索引上的索引设置不会在收缩操作期间复制。 除了不可复制的设置之外，可以通过将 URL 参数`copy_settings = true`添加到请求来将源索引中的设置复制到目标索引。 请注意，`copy_settings`不能设置为`false`。 参数 copy_settings 将在 8.0.0 中删除

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

可以使用`_cat recovery`API 监视收缩过程，或者可以使用`cluster health`API 等待，直到通过将`wait_for_status`参数设置为黄色来分配所有主分片。

在分配任何分片之前，只要目标索引已添加到群集状态，`_shrink`API 就会返回。 此时，所有分片都处于未分配的状态。 如果由于任何原因无法在收缩节点上分配目标索引，则其主分片将保持未分配状态，直到可以在该节点上分配为止。

一旦分配了主分片，它就会转到状态`初始化`，并开始缩小过程。 收缩操作完成后，分片将变为`活动`状态。 此时，Elasticsearch 将尝试分配任何副本，并可能决定将主分片重定位到另一个节点。

### 等待活动分片

由于收缩操作会创建一个新的索引来缩小分片，因此在索引创建时[wait for active shards](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/indices-create-index.html#create-index-wait-for-active-shards)设置也适用于缩减索引操作。

## 拆分索引(Split Index)

拆分索引 API 允许将现有索引拆分为新索引，其中每个源主分片被拆分为两个或多个主分片

<img src="../images/attention.png" alt="attention" width=35 height=35/>`_split` API 要求使用特定的 number_of_routing_shards 创建源索引，以便将来拆分。此要求已在 Elasticsearch7.0 删除

可以拆分索引的次数(以及每个原始分片可以拆分成的分片数)由`index.number_of_routing_shards`设置确定。路由分片的数量指定内部使用的散列空间，以便在具有一致散列的分片中分发文档。例如，`number_of_routing_shards`设置 30(5x2x3)的 5 个分片索引可以按因子 2 或 3 分割。

- `5 -> 10 -> 30` (先以 2 拆分，然后以 3 拆分)
- `5 -> 15 -> 30` (先以 3 拆分，然后以 2 才分)
- `5 -> 30` (以 6 拆分)

### 拆分索引的工作原理

- 首先，创建一个与源索引相同定义的新的目标索引，但主分片比源索引更多
- 然后，将源索引的片段硬链接到目标索引。（如果文件系统不支持硬链接，则会将所有段复制到新索引中，这是一个更耗时的过程）
- 创建低级文件后，将再次对所有文档进行哈希处理，以删除属于不同分片的文档
- 最后，恢复目标索引，类似于一个刚刚重新打开的封闭索引

### 为什么 ES 不支持增量重新分片

增量重新分片(`N->N+1`)，添加新分片并将新数据推送到新分片，这可能带来性能瓶颈，并且在给定其`_id`(这是`get`，`delete`，`update`所必须的)是，确定文档属于哪个分片会变得非常复杂。这意味着我们需要使用不同的散列方案重新平衡现有数据

键值存储最有效地实现此目的的方法是使用一致的 HASH 散列。当从`N`增加到`N+1`，一致 HASH 只需要重新定位键的`1/N`。不过 ES 的`存储单元`,`分片`是 Lucene 索引。由于采用面向搜索的数据结构，占据了 Lucene 索引的很大一部分，只有 5%的文档，删除它们并在另一个分片上索引它们通常会比使用键值存储的成本高很多。通过乘法因子增加分片数量时，此成本保持合理，如上一节所述：这允许 Elasticsearch 在本地执行拆分，从而允许在索引级别执行拆分，而不是重新索引需要移动的文档，以及使用硬链接进行有效的文件复制。

在仅附加数据的情况下，可以通过创建新索引并向其推送新数据来获得更大的灵活性，同时添加用于覆盖读取操作的旧索引和新索引的别名。假设旧索引和新索引分别具有 M 和 N 个分片，与搜索具有 M+N 个分片的索引相比，这个没有开销

### 拆分索引实例

#### 准备一个用于拆分的索引

```json
PUT my_source_index
{
  "setting": {
    "index.number_of_shards": 1,
    "index.number_of_routing_shards": 2
  }
}
```

`"index.number_of_routing_shards": 2`允许将索引拆分为两个分片，换句话说，它允许单个分割操作

为了拆分索引，必须将其标记为`只读`，并且索引状态应为`green`

```json
PUT /my_source_index/_settings
{
  "settings": {
    "index.blocks.write": true
  }
}
```

`"index.blocks.write": true`阻止对此索引的写入操作，同时仍允许更改元数据，如删除索引。

#### 拆分该索引

```json
POST my_source_index/_split/my_target_index?copy_settings=true
{
  "settings": {
    "index.number_of_shards": 2
  }
}
```

将目标索引添加到群集状态后，上述请求会立即返回——它不会等待分割操作开始。

<img src="../images/attention.png" alt="attention" width=35 height=35/>索引只有满足以下要求才能拆分：

- 目标索引不存在
- 源索引的分片数必须比目标索引少
- 目标索引中的主分片数必须是源索引中主分片数的一个因子
- 处理拆分进程的节点必须具有足够的可用磁盘空间，以容纳现有索引的第二个副本。

`_split API`类似于`create index API`，并接受目标索引的设置和别名参数：

```json
POST my_source_index/_split/my_target_index?copy_settings=true
{
  "settings": {
    "index.number_of_shards": 5
  },
  "aliases": {
    "my_search_indices": {}
  }
}
```

其他相关内容如`监控拆分过程`, `等待活动分片`和`收缩分片`相同

## 翻转索引(Rollover Index)

当现有索引被认为太大或太旧时，`翻转索引 API` 将别名滚动到新索引

API 接受单个别名和`conditions`列表。别名必须指向 Rollover 请求的写入索引才有效，有两种方法可以实现，并且根据配置，别名元数据将以下不同方式更新。这两种情况如下：

- 别名仅指向未配置`is_write_index`的单个索引(默认为`null`)

在这种情况下，原始索引将其翻转别名添加到新创建的索引中，并从原始(翻转)索引中删除。

- 别名指向一个或多个索引，其中`is_write_index`在要翻转的索引(写入索引)上设置为`true`

在这种情况下，写入索引将其翻转别名的`is_write_index`设置为`false`，而新创建的索引现在具有指向它的翻转别名作为写入索引，其中`is_write_index`为`true`。

可用条件如下：

| 参数名   | 描述                                             |
| -------- | ------------------------------------------------ |
| max_age  | 索引的最大周期                                   |
| max_docs | 索引应包含的最大文档数。这不会为副本多次添加文档 |
| max_size | 索引的主分片的最大估计大小                       |

```json

# 创建一个logs-000001，且别名为logs_write的索引
PUT /logs-000001
{
  "aliases": {
    "logs_write": {}
  }
}

# Add > 1000 documents to logs-000001

# 如果logs_write指向的索引时7天或更长时间以前创建的，或者包含1000个或更多的文档，或索引大小至少约5GB，则会创建logs-000002索引，并更新别名logs_write指向logs-000002
POST /logs_write/_rollover
{
  "conditions": {
    "max_age":   "7d",
    "max_docs":  1000,
    "max_size":  "5gb"
  }
}

resp=>
{
  "acknowledged": true,
  "shards_acknowledged": true,
  "old_index": "logs-000001",
  "new_index": "logs-000002",
  "rolled_over": true, //索引是否已经滚动
  "dry_run": false, //索引是否空运行
  "conditions": { //每个条件的结果
    "[max_age: 7d]": false,
    "[max_docs: 1000]": true,
    "[max_size: 5gb]": false,
  }
}
```

### 命名新索引

如果现有索引名称以`-`和一个数字组成，例如，`logs-000001`是旧索引名，新索引遵从前者的命名规则，新建索引名为`logs-000002`。无论旧索引名称如何，该数字均为零填充，长度为 6。如果旧名称与此模式不匹配，则必须指定新索引的名称

```json
POST /my_alias/_rollover/my_new_index_name
{
  "conditions": {
    "max_age":   "7d",
    "max_docs":  1000,
    "max_size": "5gb"
  }
}
```

### 在翻转 API 中使用 Date Math

可以根据索引的翻转日期，使用[`date math`](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/date-math-index-names.html)来命名翻转索引。例如，logstash-2016.02.03。`rollover API`支持 date math，但要求索引名称以短划线后跟数字结尾。

```json
# PUT /<logs-{now/d}-1> with URI encoding:
# 创建一个以今天的日期命名的索引（例如）logs-2016.10.31-1
PUT /%3Clogs-%7Bnow%2Fd%7D-1%3E
{
  "aliases": {
    "logs_write": {}
  }
}

PUT logs_write/_doc/1
{
  "message": "a dummy log"
}

POST logs_write/_refresh

# Wait for a day to pass
# 使用今天的日期翻转到新索引，例如如果立即运行，则记录为2016.10.31-000002;如果在24小时后运行，则记录为2016.11.01-000002
POST /logs_write/_rollover
{
  "conditions": {
    "max_docs":   "1"
  }
}
```

<img src="../images/attention.png" alt="attention" width=35 height=35/>关于`date math`可以参考[`date math`](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/date-math-index-names.html)

举个例子：

```json
# 搜索过去三天创建的索引
# GET /<logs-{now/d}-*>,<logs-{now/d-1d}-*>,<logs-{now/d-2d}-*>/_search
GET /%3Clogs-%7Bnow%2Fd%7D-*%3E%2C%3Clogs-%7Bnow%2Fd-1d%7D-*%3E%2C%3Clogs-%7Bnow%2Fd-2d%7D-*%3E/_search
```

### 定义新索引

新索引的设置(`settings`)，映射(`mappings`)和别名(`aliases`)取自任何匹配的`索引模板`，此外，就像创建索引一样，你可以在请求的 body 中指定`settings`，`mappings`和`aliases`，请求中指定的值将覆盖匹配的索引模板中设置的任何值(`request>template`)。

例如，以下翻转请求会覆盖 index.number_of_shards 设置：

```json
PUT /logs-000001
{
  "aliases": {
    "logs_write": {}
  }
}

POST /logs_write/_rollover
{
  "conditions" : {
    "max_age": "7d",
    "max_docs": 1000,
    "max_size": "5gb"
  },
  "settings": {
    "index.number_of_shards": 2
  }
}
```

### 空运行(Dry Run)

rollover API 支持`dry_run`模式，可以在不执行实际翻转的情况下检查请求条件

```json
PUT /logs-000001
{
  "aliases": {
    "logs_write": {}
  }
}

POST /logs_write/_rollover?dry_run
{
  "conditions" : {
    "max_age": "7d",
    "max_docs": 1000,
    "max_size": "5gb"
  }
}
```

### Rollover API——等待活动分片

由于翻转操作会创建一个新的索引以进行翻转，因此索引创建的`wait_for_active_shards`设置也适用于翻转操作。

### 写索引别名行为(Write Index Alias Behavior)

当在具有`is_write_index`显式设置为`true`的写索引上翻转时，不会再翻转操作期间交换翻转别名。由于具有多个索引的别名在区分哪个是正确的翻转写入索引时是不明确的，因此翻转指向多个索引的别名是无效的。因此，默认行为是交换面向写入别名指向的索引。由于设置 is_write_index 使别名能够指向多个索引，同时还明确指出 rollover 应该针对哪个写入索引，因此不需要从滚动索引中删除别名。这通过允许一个别名表现为使用 Rollover 管理的索引的写入和读取别名来简化事情。

```json
PUT my_logs_index-000001
{
  "aliases": {
    "logs": { "is_write_index": true } //将my_logs_index配置为日志别名的写入索引
  }
}

PUT logs/_doc/1
{
  "message": "a dummy log"
}

POST logs/_refresh

POST /logs/_rollover
{
  "conditions": {
    "max_docs":   "1"
  }
}

# 别名已经重新指向新索引
PUT logs/_doc/2
{
  "message": "a newer log"
}

resp=>
{
  "_index" : "my_logs_index-000002",
  "_type" : "_doc",
  "_id" : "2",
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
```

在翻转之后，两个索引的别名元数据将使`is_write_index`设置反映每个索引的角色，新创建的索引作为写入索引。

```json
{
  "my_logs_index-000002": {
    "aliases": {
      "logs": { "is_write_index": true }
    }
  },
  "my_logs_index-000001": {
    "aliases": {
      "logs": { "is_write_index": false }
    }
  }
}
```
