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
  - [设置映射(PUT Mapping)](#%e8%ae%be%e7%bd%ae%e6%98%a0%e5%b0%84put-mapping)
    - [多索引(Multi-index)](#%e5%a4%9a%e7%b4%a2%e5%bc%95multi-index)
    - [更新字段映射(Update field mappings)](#%e6%9b%b4%e6%96%b0%e5%ad%97%e6%ae%b5%e6%98%a0%e5%b0%84update-field-mappings)
    - [跳过 types](#%e8%b7%b3%e8%bf%87-types-1)
  - [获取映射(Get Mapping)](#%e8%8e%b7%e5%8f%96%e6%98%a0%e5%b0%84get-mapping)
    - [多 indices 和 types](#%e5%a4%9a-indices-%e5%92%8c-types)
  - [获取字段映射(Get Field Mapping)](#%e8%8e%b7%e5%8f%96%e5%ad%97%e6%ae%b5%e6%98%a0%e5%b0%84get-field-mapping)
    - [多索引，类型和字段(Multiple Indices, Types and Fields)](#%e5%a4%9a%e7%b4%a2%e5%bc%95%e7%b1%bb%e5%9e%8b%e5%92%8c%e5%ad%97%e6%ae%b5multiple-indices-types-and-fields)
    - [指定字段(Specifying fields)](#%e6%8c%87%e5%ae%9a%e5%ad%97%e6%ae%b5specifying-fields)
    - [其他选项](#%e5%85%b6%e4%bb%96%e9%80%89%e9%a1%b9)
  - [类型存在检查(Types Exists)](#%e7%b1%bb%e5%9e%8b%e5%ad%98%e5%9c%a8%e6%a3%80%e6%9f%a5types-exists)
  - [索引别名(Index Aliases)](#%e7%b4%a2%e5%bc%95%e5%88%ab%e5%90%8dindex-aliases)
    - [过滤别名(Filter Aliases)](#%e8%bf%87%e6%bb%a4%e5%88%ab%e5%90%8dfilter-aliases)
    - [路由(Routing)](#%e8%b7%af%e7%94%b1routing)
    - [写索引(Write Index)](#%e5%86%99%e7%b4%a2%e5%bc%95write-index)
    - [添加单个别名(Add a single alias)](#%e6%b7%bb%e5%8a%a0%e5%8d%95%e4%b8%aa%e5%88%ab%e5%90%8dadd-a-single-alias)
    - [索引创建期间的别名(Aliases during index creation)](#%e7%b4%a2%e5%bc%95%e5%88%9b%e5%bb%ba%e6%9c%9f%e9%97%b4%e7%9a%84%e5%88%ab%e5%90%8daliases-during-index-creation)
    - [删除别名(Delete aliases)](#%e5%88%a0%e9%99%a4%e5%88%ab%e5%90%8ddelete-aliases)
    - [检索现有别名(Retrieving existing aliases)](#%e6%a3%80%e7%b4%a2%e7%8e%b0%e6%9c%89%e5%88%ab%e5%90%8dretrieving-existing-aliases)
  - [更新索引设置(Update Indices Settings)](#%e6%9b%b4%e6%96%b0%e7%b4%a2%e5%bc%95%e8%ae%be%e7%bd%aeupdate-indices-settings)
    - [批量索引使用(Bluk Indexing Usage)](#%e6%89%b9%e9%87%8f%e7%b4%a2%e5%bc%95%e4%bd%bf%e7%94%a8bluk-indexing-usage)
    - [更新索引分析(Updating Index Analysis)](#%e6%9b%b4%e6%96%b0%e7%b4%a2%e5%bc%95%e5%88%86%e6%9e%90updating-index-analysis)
  - [获取设置](#%e8%8e%b7%e5%8f%96%e8%ae%be%e7%bd%ae)
    - [通过名称过滤设置](#%e9%80%9a%e8%bf%87%e5%90%8d%e7%a7%b0%e8%bf%87%e6%bb%a4%e8%ae%be%e7%bd%ae)
  - [分析(Analyze)](#%e5%88%86%e6%9e%90analyze)
  - [解释分析(Explain Analyze)](#%e8%a7%a3%e9%87%8a%e5%88%86%e6%9e%90explain-analyze)
  - [索引模板(Index Templates)](#%e7%b4%a2%e5%bc%95%e6%a8%a1%e6%9d%bfindex-templates)
    - [删除模板(Deleting a Template)](#%e5%88%a0%e9%99%a4%e6%a8%a1%e6%9d%bfdeleting-a-template)
    - [获取模板(Getting templates)](#%e8%8e%b7%e5%8f%96%e6%a8%a1%e6%9d%bfgetting-templates)
    - [模板存在(Template exists)](#%e6%a8%a1%e6%9d%bf%e5%ad%98%e5%9c%a8template-exists)
    - [多模板匹配(Multiple Templates Matching)](#%e5%a4%9a%e6%a8%a1%e6%9d%bf%e5%8c%b9%e9%85%8dmultiple-templates-matching)
    - [模板版本](#%e6%a8%a1%e6%9d%bf%e7%89%88%e6%9c%ac)
  - [索引统计(Indices Stats)](#%e7%b4%a2%e5%bc%95%e7%bb%9f%e8%ae%a1indices-stats)
  - [索引片段(Indices Segments)](#%e7%b4%a2%e5%bc%95%e7%89%87%e6%ae%b5indices-segments)
    - [详细模式(Verbose mode)](#%e8%af%a6%e7%bb%86%e6%a8%a1%e5%bc%8fverbose-mode)
  - [索引恢复(Indices Recovery)](#%e7%b4%a2%e5%bc%95%e6%81%a2%e5%a4%8dindices-recovery)
  - [索引分片存储(Indices Shard Stores)](#%e7%b4%a2%e5%bc%95%e5%88%86%e7%89%87%e5%ad%98%e5%82%a8indices-shard-stores)
  - [清除缓存(Clear Cache)](#%e6%b8%85%e9%99%a4%e7%bc%93%e5%ad%98clear-cache)
  - [清除(Flush)](#%e6%b8%85%e9%99%a4flush)
    - [同步清除(Synced Flush)](#%e5%90%8c%e6%ad%a5%e6%b8%85%e9%99%a4synced-flush)
      - [同步清除 API(Synced Flush API)](#%e5%90%8c%e6%ad%a5%e6%b8%85%e9%99%a4-apisynced-flush-api)
  - [刷新(Refresh)](#%e5%88%b7%e6%96%b0refresh)
  - [强制合并(Force Merge)](#%e5%bc%ba%e5%88%b6%e5%90%88%e5%b9%b6force-merge)

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

<img src="../images/attention.png" alt="attention" width=35 height=35/>\_split API 要求使用特定的 number_of_routing_shards 创建源索引，以便将来拆分。此要求已在 Elasticsearch7.0 删除

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

当现有索引被认为太大或太旧时，`翻转索引 API` 将别名移到新索引

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

## 设置映射(PUT Mapping)

`PUT Mapping API`允许将字段添加到现有索引或仅更改现有字段的搜索设置。

```json
// 创建一个不含任何类型的索引
PUT twitter
{}

// 使用PUT映射API将名为email的新字段添加到_doc映射类型
PUT twitter/_mapping/_doc
{
  "properties": {
    "email": {
      "type": "keyword"
    }
  }
}
```

### 多索引(Multi-index)

`PUT 映射API`可以通过单个请求应用于多个索引

```json
// 创建两个索引
PUT twitter-1
PUT twitter-2

// 修改索引的映射，支持通配符
PUT /twitter-1,twitter-2/_mapping/_doc
{
  "properties": {
    "user_name": {
      "type": "text"
    }
  }
}
```

<img src="../images/note.png" alt="attention" width=25 height=25/>使用`PUT Mapping API`更新`_default_`时，新的`mapping`不与现有的`mapping`合并，而是用新的`mapping`替换现有的`mapping`

### 更新字段映射(Update field mappings)

通常，无法更新现有字段的映射，但也有些例外情况

- 新的`properties`能被添加到`Object`数据类型字段
- 可以将新的`multi-fields`添加到现有字段中
- `ignore_above`参数可以更新

例子：

```json
PUT my_index
{
  "mappings": {
    "_doc": {
      "properties": {
        "name": {
          "properties": {
            "first": {
              "type": "text"
            }
          }
        },
        "user_id": {
          "type": "keyword"
        }
      }
    }
  }
}

// 在name对象字段下添加字段last
// 修改ignore_above设置，默认为0
PUT my_index/_mapping/_doc
{
  "properties": {
    "name": {
      "properties": {
        "last": {
          "type": "text"
        }
      }
    },
    "user_id": {
      "type": "keyword",
      "ignore_above": 100
    }
  }
}
```

### 跳过 types

Types 从 es7.0 删除，默认情况下，`mappings`元素将不再将`type`名称作为顶级键,可以通过设置`include_type_name = false`并将映射直接放在索引创建调用中的映射下来选择加入此行为，而不用指定`type`名称。

<img src="../images/note.png" alt="attention" width=25 height=25/>对于在 Elasticsearch 5.x 上创建的索引，此类调用将实际引入或更新\_doc 类型的映射。建议避免在 5.x 索引上使用 include_type_name = false 调用 put-mapping API。

例子：

```json
PUT my_index?include_type_name=false
{
  "mappings": {
    "properties": {
      "name": {
        "properties": {
          "first": {
            "type": "text"
          }
        }
      },
      "user_id": {
        "type": "keyword"
      }
    }
  }
}
```

## 获取映射(Get Mapping)

`GET Mapping API` 能获取`index`或`index/type`的`mapping`的定义

### 多 indices 和 types

```json

GET /_mapping/_doc

GET /_all/_mapping/_doc

GET /indexA,indexB/_mapping/_doc

GET /_all/_mapping

GET /_mapping

```

## 获取字段映射(Get Field Mapping)

使用获取字段映射 API，可以检索一个或多个字段的映射定义

```json

//创建索引
PUT publications
{
    "mappings": {
        "_doc": {
            "properties": {
                "id": { "type": "text" },
                "title":  { "type": "text"},
                "abstract": { "type": "text"},
                "author": {
                    "properties": {
                        "id": { "type": "text" },
                        "name": { "type": "text" }
                    }
                }
            }
        }
    }
}

//仅返回字段title的mapping
GET publications/_mapping/_doc/field/title

//响应如下:
{
   "publications": {
      "mappings": {
         "_doc": {
            "title": {
               "full_name": "title",
               "mapping": {
                  "title": {
                     "type": "text"
                  }
               }
            }
         }
      }
   }
}
```

### 多索引，类型和字段(Multiple Indices, Types and Fields)

获取字段映射 API 可用于通过一次调用从多个索引或类型中获取多个字段的映射。API 的一般用法遵循以下语法：host:port/{index}/{type}/\_mapping/field/{field}, 其中{index}，{type}和{field}可以代表逗号分隔的名称列表或通配符。要获取所有索引的映射，可以将\_all 用于{index}。以下是一些示例：

```json
GET /twitter,kimchy/_mapping/field/message

GET /_all/_mapping/_doc/field/message,user.id

GET /_all/_mapping/_doc/field/*.id

```

### 指定字段(Specifying fields)

使用 get mapping API，可以指定以逗号分隔的字段列表，例如，选择`author`字段的`id`，必须使用它的全名`author.id`。

```json
GET publications/_mapping/_doc/field/author.id,abstract,name

//响应如下
{
   "publications": {
      "mappings": {
         "_doc": {
            "author.id": {
               "full_name": "author.id",
               "mapping": {
                  "id": {
                     "type": "text"
                  }
               }
            },
            "abstract": {
               "full_name": "abstract",
               "mapping": {
                  "abstract": {
                     "type": "text"
                  }
               }
            }
         }
      }
   }
}

//通配符
GET publications/_mapping/_doc/field/a*

//响应如下
{
   "publications": {
      "mappings": {
         "_doc": {
            "author.name": {
               "full_name": "author.name",
               "mapping": {
                  "name": {
                     "type": "text"
                  }
               }
            },
            "abstract": {
               "full_name": "abstract",
               "mapping": {
                  "abstract": {
                     "type": "text"
                  }
               }
            },
            "author.id": {
               "full_name": "author.id",
               "mapping": {
                  "id": {
                     "type": "text"
                  }
               }
            }
         }
      }
   }
}
```

### 其他选项

`include_defaults`：向查询字符串中添加`include_defaults=true`将导致响应包含默认值，通常会禁止这些默认值。

```json
GET publications/_mapping/field/id?include_defaults=true

//响应如下
{
  "publications" : {
    "mappings" : {
      "_doc" : {
        "id" : {
          "full_name" : "id",
          "mapping" : {
            "id" : {
              "type" : "text",
              "boost" : 1.0,
              "index" : true,
              "store" : false,
              "doc_values" : false,
              "term_vector" : "no",
              "norms" : true,
              "index_options" : "positions",
              "eager_global_ordinals" : false,
              "similarity" : "BM25",
              "analyzer" : "default",
              "search_analyzer" : "default",
              "search_quote_analyzer" : "default",
              "include_in_all" : true,
              "position_increment_gap" : -1,
              "fielddata" : false
            }
          }
        }
      }
    }
  }
}
```

## 类型存在检查(Types Exists)

HTTP 状态码指示当前类型是否存在，404 表示不存在，200 表示存在

```json
HEAD twitter/_mapping/tweet
```

## 索引别名(Index Aliases)

索引别名 API 允许对索引使用别名，且所有的 API 都会自动将别名转换为实际的索引名。别名也可以映射到多个索引，并且在指定别名时，别名将自动扩展到被设置别名的索引。别名也可以和过滤器关联，该过滤器将在搜索和路由取值时自动应用。别名不能同索引同名。

```json

//添加别名
POST /_aliases
{
    "actions" : [
        { "add" : { "index" : "test1", "alias" : "alias1" } }
    ]
}

//删除别名
POST /_aliases
{
    "actions" : [
        { "remove" : { "index" : "test1", "alias" : "alias1" } }
    ]
}
```

重命名别名是在相同 API 中的简单删除然后添加操作。此操作是原子操作，无需担心别名不指向索引的短时间

```json
POST /_aliases
{
    "actions" : [
        { "remove" : { "index" : "test1", "alias" : "alias1" } },
        { "add" : { "index" : "test2", "alias" : "alias1" } }
    ]
}


//将别名与多个索引相关联只是几个添加操作
POST /_aliases
{
    "actions" : [
        { "add" : { "index" : "test1", "alias" : "alias1" } },
        { "add" : { "index" : "test2", "alias" : "alias1" } }
    ]
}

//可以使用索引的数组添加别名
POST /_aliases
{
    "actions" : [
        { "add" : { "indices" : ["test1", "test2"], "alias" : "alias1" } }
    ]
}

//使用别名的数组为索引添加别名
POST /_aliases
{
    "actions" : [
        { "add" : { "index" : "test1", "aliases" : ["alias1", "alias2"] } }
    ]
}

//通配符设置索引别名
POST /_aliases
{
    "actions" : [
        { "add" : { "index" : "test*", "alias" : "all_test_indices" } }
    ]
}

//remove_index == delete index
PUT test
PUT test_2
POST /_aliases
{
    "actions" : [
        { "add":  { "index": "test_2", "alias": "test" } },
        { "remove_index": { "index": "test" } }
    ]
}
```

### 过滤别名(Filter Aliases)

可以使用`Query DSL`定义过滤器，并使用此别名将其应用于所有`搜索`、`计数`、`按查询删除`等操作

```json
//首先创建user字段
PUT /test1
{
  "mappings": {
    "_doc": {
      "properties": {
        "user" : {
          "type": "keyword"
        }
      }
    }
  }
}

//然后对user字段创建一个使用过滤器的别名
POST /_aliases
{
    "actions" : [
        {
            "add" : {
                 "index" : "test1",
                 "alias" : "alias2",
                 "filter" : { "term" : { "user" : "kimchy" } }
            }
        }
    ]
}

//响应如下
{
  "test1" : {
    "aliases" : {
      "alias2" : {
        "filter" : {
          "term" : {
            "user" : "kimchy"
          }
        }
      }
    },
    "mappings" : {
      "_doc" : {
        "properties" : {
          "user" : {
            "type" : "keyword"
          }
        }
      }
    },
    "settings" : {
      "index" : {
        "creation_date" : "1575106238708",
        "number_of_shards" : "5",
        "number_of_replicas" : "1",
        "uuid" : "9TWMYsuDQwyiy1ub6y3V3g",
        "version" : {
          "created" : "6080299"
        },
        "provided_name" : "test1"
      }
    }
  }
}
```

### 路由(Routing)

可以将路由的值和别名相关联，此功能可以与过滤别名一起使用，以避免不必要的分片操作。

```json
POST /_aliases
{
    "actions" : [
        {
            "add" : {
                 "index" : "test",
                 "alias" : "alias1",
                 "routing" : "1"
            }
        }
    ]
}

//可以为搜索和索引操作指定不同的路由值
POST /_aliases
{
    "actions" : [
        {
            "add" : {
                 "index" : "test",
                 "alias" : "alias2",
                 "search_routing" : "1,2",
                 "index_routing" : "2"
            }
        }
    ]
}

//search操作，只能(1,2)和(2,3)的交集，及2
GET /alias2/_search?q=user:kimchy&routing=2,3
```

### 写索引(Write Index)

可以使用别名 API 和索引创建 API 将与别名关联的索引指定为写索引。将索引设置为具有别名的写索引还会影响翻转索引期间别名的操作方式。`每个别名一次只能分配一个索引作为写索引`。

```json

//将别名alias1关联到test和test2，其中test是选择写入的索引
POST /_aliases
{
    "actions" : [
        {
            "add" : {
                 "index" : "test",
                 "alias" : "alias1",
                 "is_write_index" : true  //只能指定一个写索引
            }
        },
        {
            "add" : {
                 "index" : "test2",
                 "alias" : "alias1"
            }
        }
    ]
}

//添加一个doc
PUT /alias1/_doc/1
{
    "foo": "bar"
}

//获取test文档1
GET test/_doc/1
//响应如下
{
  "_index" : "test",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 2,
  "_seq_no" : 1,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "foo" : "bar"
  }
}

GET test2/_doc/1
//响应如下
{
  "_index" : "test2",
  "_type" : "_doc",
  "_id" : "1",
  "found" : false
}

//交换写索引
POST /_aliases
{
    "actions" : [
        {
            "add" : {
                 "index" : "test",
                 "alias" : "alias1",
                 "is_write_index" : false
            }
        }, {
            "add" : {
                 "index" : "test2",
                 "alias" : "alias1",
                 "is_write_index" : true
            }
        }
    ]
}
```

<img src="../images/attention.png" alt="attention" width=35 height=35/>没有为索引明确设置 is_write_index：true 的别名，并且仅引用一个索引，该别名的行为就好像是写索引，直到引用了另一个索引为止。此时，将没有写索引，并且写操作将被拒绝。

```json
//两个新创建的索引test，test2
PUT test1
PUT test2

//为test1添加别名alias1
POST /_aliases
{
  "actions": [
    {
      "add": {
        "index": "test",
        "alias": "alias1"
      }
    }
  ]
}

//插入数据doc1
PUT /alias1/_doc/1
{
    "foo": "bar1"
}

//响应如下
{
  "_index" : "test",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}

//为alias1新添一个索引test2
POST /_aliases
{
  "actions": [
    {
      "add": {
        "index": "test2",
        "alias": "alias1"
      }
    }
  ]
}

//插入doc2
PUT /alias1/_doc/2
{
    "foo": "bar2"
}
//响应如下
{
  "error": {
    "root_cause": [
      {
        "type": "illegal_argument_exception",
        "reason": "no write index is defined for alias [alias1]. The write index may be explicitly disabled using is_write_index=false or the alias points to multiple indices without one being designated as a write index"
      }
    ],
    "type": "illegal_argument_exception",
    "reason": "no write index is defined for alias [alias1]. The write index may be explicitly disabled using is_write_index=false or the alias points to multiple indices without one being designated as a write index"
  },
  "status": 400
}

//设置is_write_index
POST /_aliases
{
  "actions": [
    {
      "add": {
        "index": "test2",
        "alias": "alias1",
        "is_write_index" : true
      }
    }
  ]
}

//重新插入数据doc2
PUT /alias1/_doc/2
{
    "foo": "bar2"
}
//响应如下
{
  "_index" : "test2",
  "_type" : "_doc",
  "_id" : "2",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}
```

### 添加单个别名(Add a single alias)

别名也可以与端点一起添加，`PUT /{index}/_alias/{name}`

| 参数    | 描述                                                                  |
| ------- | --------------------------------------------------------------------- |
| index   | 别名引用的索引。可以是\* \| \_all \| glob pattern \| name1, name2,... |
| name    | 别名的名称。这是必需的选项。                                          |
| routing | 可以与别名关联的可选路由。                                            |
| filter  | 可以与别名关联的可选过滤器。                                          |

例子：

```json
//添加基于时间的别名
PUT /logs_201305/_alias/2013

//添加用户别名
//首先创建索引并为user_id字段添加一个映射
PUT /users
{
    "mappings" : {
        "_doc" : {
            "properties" : {
                "user_id" : {"type" : "integer"}
            }
        }
    }
}
//然后为特定用户添加别名
PUT /users/_alias/user_12
{
    "routing" : "12",
    "filter" : {
        "term" : {
            "user_id" : 12
        }
    }
}

//插入测试数据doc1
PUT users/_doc/1
{
  "user_id":1
}

GET /user_12/_doc/1
//响应
{
  "_index" : "users",
  "_type" : "_doc",
  "_id" : "1",
  "found" : false
}

//插入测试数据doc12
PUT users/_doc/12
{
  "user_id":12
}

GET /user_12/_doc/12
//响应
{
  "_index" : "users",
  "_type" : "_doc",
  "_id" : "12",
  "_version" : 1,
  "_seq_no" : 0,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "user_id" : 12
  }
}
```

### 索引创建期间的别名(Aliases during index creation)

别名也可以在创建索引期间指定

```json
PUT /logs_20162801
{
    "mappings" : {
        "_doc" : {
            "properties" : {
                "year" : {"type" : "integer"}
            }
        }
    },
    "aliases" : {
        "current_day" : {},
        "2016" : {
            "filter" : {
                "term" : {"year" : 2016 }
            }
        }
    }
}
```

### 删除别名(Delete aliases)

`DELETE /{index}/_alias/{name}`

可选项：

| 参数  | 描述                                           |
| ----- | ---------------------------------------------- |
| index | \* \| \_all \| glob pattern \| name1,name2,... |
| name  | \* \| \_all \| glob pattern \| name1,name2,... |

```json
DELETE / logs_20162801 / _alias / current_day
```

### 检索现有别名(Retrieving existing aliases)

请求路径模式为：`/{index}/_alias/{alias}`。

获取索引别名 API 允许按别名和索引名称进行过滤。此 api 重定向到主服务器，并获取请求的索引别名（如果有）。此 api 仅序列化找到的索引别名

可选项：

| 参数               | 描述                                                                                                           |
| ------------------ | -------------------------------------------------------------------------------------------------------------- |
| index              | 要为其获取别名的索引名称。通过通配符支持部分名称，也可以指定多个索引名称，并用逗号分隔。也可以使用索引的别名。 |
| alias              | 要在响应中返回的别名的名称。与索引选项一样，此选项支持通配符，该选项指定多个别名，以逗号分隔。                 |
| ignore_unavailable | 如果指定的索引名称不存在，该怎么办。如果设置为 true，则将忽略那些索引。                                        |

例子：

```json
GET /logs_20162801/_alias/*
GET /_alias/20*
HEAD /_alias/2016
HEAD /_alias/20*
HEAD /logs_20162801/_alias/*
```

## 更新索引设置(Update Indices Settings)

`/_settings`可用于更新所有索引，或者`{index}/_settings`更新一个或多个索引的设置

```json
PUT /twitter/_settings
{
    "index" : {
        "number_of_replicas" : 2
    }
}
```

要将设置重置为默认值，请使用 `null`

```json
PUT /twitter/_settings
{
    "index" : {
        "refresh_interval" : null
    }
}
```

可以在[Index Modules](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/index-modules.html)中查阅相关设置。为了·防止现有设置被更新，可以设置`preserve_existing:true`

### 批量索引使用(Bluk Indexing Usage)

例子：
更新设置的 API 可用于动态更改索引，使其对批量索引的性能更高，然后将其移至更实时的索引状态。在开始批量索引编制之前，请使用：

```json
PUT /twitter/_settings
{
    "index" : {
        "refresh_interval" : "-1"
    }
}
```

(另一个优化选项是在没有任何副本的情况下启动索引，并且仅在以后添加它们，但这实际上取决于用例)

然后，一旦完成批量索引的编制，就可以更新设置了：

```json
PUT /twitter/_settings
{
    "index" : {
        "refresh_interval" : "1s"
    }
}
```

并且，一个强制合并被调用：

```json
POST /twitter/_forcemerge?max_num_segments=5
```

### 更新索引分析(Updating Index Analysis)

可以为索引定义新的分析器(`analyzers`)，但需要`先关闭索引`，然后`再进行修改`。

例子：

```json
//close index
POST /twitter/_close

//update analyzer
PUT /twitter/_settings
{
  "analysis" : {
    "analyzer":{
      "content":{
        "type":"custom",
        "tokenizer":"whitespace"
      }
    }
  }
}

//open index
POST /twitter/_open
```

## 获取设置

格式：`GET /{index}/_settings`，可以使用`通配符(*)、(_all)、(name1,name2,...)`

```json
GET / twitter / _settings
```

### 通过名称过滤设置

可以使用通配符匹配过滤返回的设置，如下所示：

```json
(GET / test,
(test2 / _settings / index.number) *
  //响应如下
  {
    "test2": {
      "settings": {
        "index": {
          "number_of_shards": "5",
          "number_of_replicas": "1"
        }
      }
    },
    "test": {
      "settings": {
        "index": {
          "number_of_shards": "5",
          "number_of_replicas": "1"
        }
      }
    }
  })
```

## 分析(Analyze)

对文本执行分析过程并返回分解的文本标记(tokens)

可以在不指定针对多个内置分析器之一的索引的情况下使用：

```json
GET _analyze
{
  "analyzer" : "standard",
  "text" : "this is a test"
}
//响应
{
  "tokens" : [
    {
      "token" : "this",
      "start_offset" : 0,
      "end_offset" : 4,
      "type" : "<ALPHANUM>",
      "position" : 0
    },
    {
      "token" : "is",
      "start_offset" : 5,
      "end_offset" : 7,
      "type" : "<ALPHANUM>",
      "position" : 1
    },
    {
      "token" : "a",
      "start_offset" : 8,
      "end_offset" : 9,
      "type" : "<ALPHANUM>",
      "position" : 2
    },
    {
      "token" : "test",
      "start_offset" : 10,
      "end_offset" : 14,
      "type" : "<ALPHANUM>",
      "position" : 3
    }
  ]
}
```

如果将 text 参数作为字符串数组提供，则将其作为多值字段进行分析。

```json
GET _analyze
{
  "analyzer" : "standard",
  "text" : ["this is a test", "the second text"]
}
//响应
{
  "tokens" : [
    {
      "token" : "this",
      "start_offset" : 0,
      "end_offset" : 4,
      "type" : "<ALPHANUM>",
      "position" : 0
    },
    ...省略...
    {
      "token" : "the",
      "start_offset" : 15,
      "end_offset" : 18,
      "type" : "<ALPHANUM>",
      "position" : 4
    },
    {
      "token" : "second",
      "start_offset" : 19,
      "end_offset" : 25,
      "type" : "<ALPHANUM>",
      "position" : 5
    },
    {
      "token" : "text",
      "start_offset" : 26,
      "end_offset" : 30,
      "type" : "<ALPHANUM>",
      "position" : 6
    }
  ]
}
```

或者通过使用分词器(`tokenizers`)、标记过滤器(`token filters`)和`char filters`构建自定义的瞬态分析器(`transient analyzer`), Token filters 可以使用较短的过滤参数名称：

```json
GET _analyze
{
  "tokenizer" : "keyword",
  "filter" : ["lowercase"],
  "text" : "this is a test"
}

GET _analyze
{
  "tokenizer" : "keyword",
  "filter" : ["lowercase"],
  "char_filter" : ["html_strip"],
  "text" : "this is a <b>test</b>"
}
```

<img src="../images/attention.png" alt="attention" width=35 height=35/>

`在 5.0.0 中弃用`，使用 filter/char_filter 替代 filters/char_filters 并且 token_filters 被移除

可以在请求主体中指定自定义分词器，标记过滤器和字符过滤器，如下所示：

```json
GET _analyze
{
  "tokenizer" : "whitespace",
  "filter" : ["lowercase", {"type": "stop", "stopwords": ["a", "is", "this"]}],
  "text" : "this is a test"
}
```

也可以针对指定索引使用：

```json
GET analyze_sample/_analyze
{
  "text" : "this is a test"
}

//指定一个分析器
GET analyze_sample/_analyze
{
  "analyzer" : "whitespace",
  "text" : "this is a test"
}

//可以基于一个字段映射派生出一个分析器，以下例子将用基于obj1.field1的映射中配置的分析器（如果没有，默认索引分析器）进行分析。
GET analyze_sample/_analyze
{
  "field" : "obj1.field1",
  "text" : "this is a test"
}
```

可以为关键字字段提供一个规范化器(normalizer)，该规范化器具有与 analyzer_sample 索引相关联的规范化器：

```json
GET analyze_sample/_analyze
{
  "normalizer" : "my_normalizer",
  "text" : "BaR"
}
```

或者通过 token filters 和 char filters 构建自定义的瞬时规范化器(transient normalizer)

```json
GET _analyze
{
  "filter" : ["lowercase"],
  "text" : "BaR"
}
```

## 解释分析(Explain Analyze)

如果要获取更多高级详细信息，请设置`explain:true`（默认为 false）。它将输出每个 token 的所有 token 属性。您可以通过设置`attributes`选项来过滤要输出的 token 属性。

```json
GET _analyze
{
  "tokenizer" : "standard",
  "filter" : ["snowball"],
  "text" : "detailed output",
  "explain" : true,
  "attributes" : ["keyword"] //设置仅输出’keyword‘属性
}
//响应
{
  "detail" : {
    "custom_analyzer" : true,
    "charfilters" : [ ],
    "tokenizer" : {
      "name" : "standard",
      "tokens" : [
        {
          "token" : "detailed",
          "start_offset" : 0,
          "end_offset" : 8,
          "type" : "<ALPHANUM>",
          "position" : 0
        },
        {
          "token" : "output",
          "start_offset" : 9,
          "end_offset" : 15,
          "type" : "<ALPHANUM>",
          "position" : 1
        }
      ]
    },
    "tokenfilters" : [
      {
        "name" : "snowball",
        "tokens" : [
          {
            "token" : "detail",
            "start_offset" : 0,
            "end_offset" : 8,
            "type" : "<ALPHANUM>",
            "position" : 0,
            "keyword" : false
          },
          {
            "token" : "output",
            "start_offset" : 9,
            "end_offset" : 15,
            "type" : "<ALPHANUM>",
            "position" : 1,
            "keyword" : false
          }
        ]
      }
    ]
  }
}
```

## 索引模板(Index Templates)

索引模板可以定义在创建新索引时自动应用的模板，模板包括 settings 和 mappings，以及一个简单的用于控制是否将模板应用于新索引的模式模板。

<img src="../images/attention.png" alt="attention" width=35 height=35/>

模板仅在创建索引时应用。更改模板不会对现有索引产生影响。当使用创建索引 API 时，在创建索引调用中定义的`settings/mappings`将优先于模板中定义的所有匹配的`settings/mappings`

例子：

定义一个名为`template_1`的模板，模板模式为`te*`或`bar*`。`settings和mappings`将应用于与`te*`或`bar*`模式匹配的任何索引名称

```json
PUT _template/template_1
{
  "index_patterns": ["te*", "bar*"],
  "settings": {
    "number_of_shards": 1
  },
  "mappings": {
    "_doc": {
      "_source": {
        "enabled": false
      },
      "properties": {
        "host_name": {
          "type": "keyword"
        },
        "created_at": {
          "type": "date",
          "format": "EEE MMM dd HH:mm:ss Z yyyy"
        }
      }
    }
  }
}
```

也可以在索引模板中包括别名(在索引创建过程中，别名中的{index}占位符将被模板所应用的实际索引名替换)：

```json
PUT _template/template_1
{
    "index_patterns" : ["te*"],
    "settings" : {
        "number_of_shards" : 1
    },
    "aliases" : {
        "alias1" : {},
        "alias2" : {
            "filter" : {
                "term" : {"user" : "kimchy" }
            },
            "routing" : "kimchy"
        },
        "{index}-alias" : {}
    }
}
```

### 删除模板(Deleting a Template)

`DELETE /_template/template_name`, 只能一次删除一个模板名(','分隔符无效)，但可以使用通配符删除匹配的模板

### 获取模板(Getting templates)

```json
//获取template_1
GET /_template/template_1
//获取索引匹配temp*的模板
GET /_template/temp*
//获取template_1,template_2
GET /_template/template_1,template_2
//获取所有模板
GET /_template
```

### 模板存在(Template exists)

```json
HEAD _template/template_1
```

### 多模板匹配(Multiple Templates Matching)

多个索引模板可以潜在地与一个索引匹配，在这种情况下，settings 和 mappings 都将合并到索引的最终配置中。可以使用`order`参数控制合并顺序，先应用低的 order，然后更高的 order 覆盖它们。例如：

```json
PUT /_template/template_1
{
    "index_patterns" : ["*"],
    "order" : 0,
    "settings" : {
        "number_of_shards" : 1
    },
    "mappings" : {
        "_doc" : {
            "_source" : { "enabled" : false }
        }
    }
}

PUT /_template/template_2
{
    "index_patterns" : ["te*"],
    "order" : 1,
    "settings" : {
        "number_of_shards" : 1
    },
    "mappings" : {
        "_doc" : {
            "_source" : { "enabled" : true }
        }
    }
}
```

上面的例子中，template_1 禁止存储\_source,但是 template_2 允许存储\_source，注意，对于 mappings，合并是`deep`的，意味着可以在高阶模板上添加/覆盖基于特定对象/属性的映射，而低阶模板则提供基础。

<img src="../images/attention.png" alt="attention" width=35 height=35/>具有相同顺序值的多个匹配模板将导致不确定的合并顺序。

### 模板版本

模板可以选择添加一个任何整数值的`version`版本号，以简化外部系统的模板管理。`version`字段是完全可选的，仅用于模板的外部管理。要取消设置模板，只需要替换模板而不用指定一个。

```json
PUT /_template/template_1
{
    "index_patterns" : ["*"],
    "order" : 0,
    "settings" : {
        "number_of_shards" : 1
    },
    "version": 123
}
```

要检查`version`，可以使用`filter_path`将响应限制为仅版本：

```json
GET /_template/template_1?filter_path=*.version
//响应
{
  "template_1" : {
    "version" : 123
  }
}
```

## 索引统计(Indices Stats)

索引级别统计信息提供有关索引上发生的不同操作的统计信息，该 API 提供了有关索引级别范围的统计信息(尽管大多数统计信息也可以使用节点级别范围进行检索)

```json
GET /_stats
GET /index1,index2/_stats
```

默认情况下，将返回所有统计信息，并且还可以在 URI 中指定仅返回特定的统计信息，这些统计信息可以是以下任意一项：

| 字段          | 描述                                                                                                                                                                                       |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| docs          | 当前存在的文档的数量和已删除文档的数量（尚未合并的文档）。注意，受刷新索引的影响。                                                                                                         |
| store         | 索引的大小                                                                                                                                                                                 |
| indexing      | 索引统计信息可以与逗号分隔的`types`列表结合使用，以提供文档类型级别的统计信息。                                                                                                            |
| get           | 获取统计信息，包括缺失的统计信息。                                                                                                                                                         |
| search        | 搜索统计信息，包括建议统计信息。可以通过添加额外的`group`参数来包含自定义组的统计信息(搜索操作可以与一个或多个组相关联)。`group`参数接受逗号分隔的组名列表。使用`_all`返回所有组的统计信息 |
| segments      | 检索开放段的内存使用情况。（可选）设置`include_segment_file_size`标志，报告每个 Lucene 索引文件的汇总磁盘使用情况。                                                                        |
| completion    | 完成建议统计                                                                                                                                                                               |
| fielddata     | Fielddata 的统计                                                                                                                                                                           |
| flush         | flush 的统计信息                                                                                                                                                                           |
| merge         | merge 的统计信息                                                                                                                                                                           |
| request_cache | 分片请求缓存的统计信息。                                                                                                                                                                   |
| refresh       | refresh 的统计信息                                                                                                                                                                         |
| warmer        | Warmer 的统计信息                                                                                                                                                                          |
| translog      | Translog 的统计信息                                                                                                                                                                        |

一些统计信息允许每个字段的粒度，它接受以逗号分隔的包含字段列表。默认情况下，所有字段都包括在内：

| 字段              | 描述                                                                                       |
| ----------------- | ------------------------------------------------------------------------------------------ |
| fields            | 统计信息中包括的字段列表。除非提供了更具体的字段列表（请参阅下文），否则它将用作默认列表。 |
| completion_fields | 完成建议统计信息中将包括的字段列表。                                                       |
| fielddata_fields  | 要包含在“字段数据”统计信息中的字段列表。                                                   |

例子：

```json
# Get back stats for merge and refresh only for all indices
GET /_stats/merge,refresh
# Get back stats for type1 and type2 documents for the my_index index
GET /my_index/_stats/indexing?types=type1,type2
# Get back just search stats for group1 and group2
GET /_stats/search?groups=group1,group2
```

返回的统计信息在索引级别上进行汇总，包括`primaries`和`total`汇总，其中`primaries`是仅主分片(primary shards)的值，`total`是主分片(primary shards)和副本分片(replica shards)的值。

为了获取分片级别的统计信息，请将`level`参数设置为`shards`。

请注意，随着分片在集群中移动，其统计信息将在其他节点上创建时被清除。另一方面，即使分片“离开”节点，该节点仍将保留分片所贡献的统计信息。

## 索引片段(Indices Segments)

提供构建 Lucene 索引(分片级别)的低级别片段信息。允许用于提供有关分片和索引状态的更多信息，可能是优化信息，删除时的数据“浪费“等等。

```json
GET /test/_segments
GET /test1,test2/_segments
GET /_segments
//响应
{
  "_shards": ...
  "indices": {
    "test": {
      "shards": {
        "0": [
          {
            "routing": {
              "state": "STARTED",
              "primary": true,
              "node": "zDC_RorJQCao9xf9pg3Fvw"
            },
            "num_committed_segments": 0,
            "num_search_segments": 1,
            "segments": {
              "_0": {
                "generation": 0,
                "num_docs": 1,
                "deleted_docs": 0,
                "size_in_bytes": 3800,
                "memory_in_bytes": 1410,
                "committed": false,
                "search": true,
                "version": "7.0.0",
                "compound": true,
                "attributes": {
                }
              }
            }
          }
        ]
      }
    }
  }
}
```

| 字段            | 描述                                                                                                                                                        |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| \_0             | JSON 文档的键是段的名称。该名称用于生成文件名：分片目录中以该段名开头的所有文件都属于该段。                                                                 |
| generation      | 需要写入新段时，generation 号基本上会增加。段名称是从该 generation 号派生的。                                                                               |
| num_docs        | 此段中存储的未删除文档数。                                                                                                                                  |
| deleted_docs    | 存储在此段中的已删除文档数。如果此数字大于 0 则很好，合并此段时将回收空间。                                                                                 |
| size_in_bytes   | 该段使用的磁盘空间量（以字节为单位）。                                                                                                                      |
| memory_in_bytes | 段需要将一些数据存储到内存中才能有效地进行搜索。该数字返回用于此目的的字节数。值-1 表示 Elasticsearch 无法计算此数字。                                      |
| committed       | 该段是否已在磁盘上同步。提交的段将在硬重启后仍然存在。无需担心出现错误，未提交的段中的数据也存储在事务日志中，以便 Elasticsearch 能够在下次启动时重放更改。 |
| search          | 该段是否可搜索。值为 false 极有可能意味着该段已被写入磁盘，但此后没有刷新以使其可搜索。                                                                     |
| version         | 用来编写此段的 Lucene 版本。                                                                                                                                |
| compound        | 段是否存储在复合文件中。如果为 true，则表示 Lucene 将段中的所有文件合并为一个文件，以保存文件描述符。                                                       |
| attributes      | 包含有关是否启用高压缩的信息                                                                                                                                |

### 详细模式(Verbose mode)

若要添加可用于调试的其他信息，请使用`verbose`标志。

<img src="../images/attention.png" alt="attention" width=35 height=35/>其他详细信息的格式在 Lucene 中被标记为实验性的，将来可能会更改。

```json
GET /test/_segments?verbose=true
//响应
{
    ...
        "_0": {
            ...
            "ram_tree": [
                {
                    "description": "postings [PerFieldPostings(format=1)]",
                    "size_in_bytes": 2696,
                    "children": [
                        {
                            "description": "format 'Lucene50_0' ...",
                            "size_in_bytes": 2608,
                            "children" :[ ... ]
                        },
                        ...
                    ]
                },
                ...
                ]

        }
    ...
}
```

## 索引恢复(Indices Recovery)

索引恢复 API 提供了对正在进行的索引分片恢复的深入了解。可以报告特定索引或群集范围内的恢复状态

```json
// 索引index1，index2的恢复信息
GET index1,index2/_recovery?human

// 查看集群范围的恢复状态，只需省略索引名称。
GET /_recovery?human
```

可设置选项：

| 选项        | 说明                                                            |
| ----------- | --------------------------------------------------------------- |
| detailed    | 显示详细视图。这主要用于查看物理索引文件的恢复。默认值：false。 |
| active_only | 仅显示当前正在进行的恢复。默认值：false。                       |

输出字段说明：

| 字段                 | 描述                                                                                                                                                                                       |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| id                   | 分片 ID                                                                                                                                                                                    |
| type                 | 恢复类型：store，snapshot，replica，relocating                                                                                                                                             |
| stage                | 恢复阶段：<br>init：恢复尚未开始；<br>index：读取索引元数据并将字节从源复制到目标；<br>start：启动引擎；打开索引以供使用；<br>translog：重播事务日志；<br>finalize：清理；<br>done：完成； |
| primary              | 如果是主分片，则为 true，否则为 false                                                                                                                                                      |
| start_time           | 恢复开始的时间戳                                                                                                                                                                           |
| stop_time            | 恢复完成的时间戳                                                                                                                                                                           |
| total_time_in_millis | 恢复分片的总时间（以毫秒为单位）                                                                                                                                                           |
| source               | 恢复源：<br>存储库说明（如果从快照进行恢复）<br>否则描述源节点                                                                                                                             |
| target               | 目标节点                                                                                                                                                                                   |
| index                | 物理索引恢复统计                                                                                                                                                                           |
| translog             | 有关 translog 恢复的统计信息                                                                                                                                                               |
| start                | 有关打开和启动索引的时间的统计信息                                                                                                                                                         |

## 索引分片存储(Indices Shard Stores)

提供索引分片副本的存储信息。存储信息报告有关存在哪些节点的分片副本，分片副本分配 ID，每个分片副本的唯一标识符以及在打开分片索引时遇到的任何异常或早期引擎故障引起的异常。

默认情况下，仅列表存储具有至少一个未分配副本的分片的信息。当集群运行状况状态为黄色时，它将列出具有至少一个未分配副本的分片的存储信息。当集群运行状况状态为红色时，它将列出具有未分配主数据库的分片的存储信息。

```json
# return information of only index test
GET /test/_shard_stores

# return information of only test1 and test2 indices
GET /test1,test2/_shard_stores

# return information of all indices
GET /_shard_stores
```

可以通过`status`参数更改列出存储信息的分片范围。默认为`yellow`和`red`。黄色列表存储具有至少一个未分配副本的分片的信息，红色存储具有未分配主分片的分片的信息。使用`green`列出具有所有已分配副本的分片的存储信息。

```json
GET /_shard_stores?status=green

// 响应
{
   "indices": {
       "my-index": {
           "shards": {
              "0": { //a
                "stores": [ //b
                    {
                        "sPa3OgxLSYGvQ4oPs-Tajw": { //c
                            "name": "node_t0",
                            "ephemeral_id" : "9NlXRFGCT1m8tkvYCMK-8A",
                            "transport_address": "local[1]",
                            "attributes": {}
                        },
                        "allocation_id": "2iNySv_OQVePRX-yaRH_lQ", //d
                        "allocation" : "primary|replica|unused", //e
                        "store_exception": ... //f
                    }
                ]
              }
           }
       }
   }
}
```

上述响应中：  
a. 这个关键字是存储信息的相应分片 ID  
b. 分片的所有副本的存储信息列表  
c. 承载存储副本的节点信息，关键字是唯一节点 id  
d. 存储副本的分配 id  
e. The status of the store copy, whether it is used as a primary, replica or not used at all
f. 打开分片索引时遇到的任何异常或较早的引擎故障

## 清除缓存(Clear Cache)

该 API 允许清除所有缓存或与一个或多个索引关联的特定缓存。

```json
POST /twitter/_cache/clear

# 清除kimchy和elasticsearch的缓存
POST /kimchy,elasticsearch/_cache/clear
# 清除所有索引缓存
POST /_cache/clear
POST /_all/_cache/clear
```

该 API 默认清除所有的缓存，通过将`query`、`fielddata`、`request`url 参数设置为 true，可以显式清除特定的缓存。

```json
// 只清除查询缓存
POST /twitter/_cache/clear?query=true
// 只清除请求缓存
POST /twitter/_cache/clear?request=true
// 只清除字段数据缓存
POST /twitter/_cache/clear?fielddata=true
```

除此之外，还可以通过指定用逗号分隔的`fields` url 参数来清除与特定字段有关的所有缓存。请注意，提供的名称必须引用具体字段-不支持对象和字段别名。

```json
POST /twitter/_cache/clear?fields=foo,bar
```

## 清除(Flush)

清除 API 允许通过 API 清除一个或多个索引。索引的清除过程可确保当前仅保留在事务日志([transaction log](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/index-modules-translog.html))中的所有数据也将永久保留在 Lucene 中。这减少了恢复时间，因为在打开 Lucene 索引后，无需从事务日志中重新索引数据。默认情况下，Elasticsearch 使用试探法来根据需要自动触发清除。用户很少需要直接调用 API。

```json
POST twitter/_flush

// 多索引
POST kimchy,elasticsearch/_flush
POST _flush
POST _all/_flush
```

---

**请求参数**

| 参数            | 描述                                                                                                                                                                |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| wait_if_ongoing | 如果设置为 true，则如果已经执行了另一个清除操作，则清除操作将阻塞，直到可以执行清除为止。默认值为 false，如果另一个清除操作已在运行，则将导致在分片级别上引发异常。 |
| force           | 即使不是必需的，也就是是否必须强制执行清除，即是否不会对索引进行任何更改。如果即使没有未提交的更改也应增加事务日志 ID，这将很有用。 （此设置可以视为内部设置）      |

### 同步清除(Synced Flush)

Elasticsearch 跟踪每个分片的索引活动。 5 分钟未进行任何索引操作的碎片将自动标记为非活动状态。这为 Elasticsearch 减少了分片资源并执行一种特殊的清除（称为`synced flush`）提供了机会。同步清除将执行常规清除，然后将生成的唯一标记（sync_id）添加到所有分片。

由于在没有正在进行的索引操作时添加了同步 id 标记，因此可以将其用作检查两个分片的 lucene 索引是否相同的快速方法。此快速同步 ID 比较（如果存在）在恢复或重新启动期间使用，以跳过该过程的第一个也是最昂贵的阶段。在这种情况下，无需复制任何段文件，并且恢复的事务日志重播阶段可以立即开始。请注意，由于同步 ID 标记是与清除一起应用的，因此事务日志很可能为空，从而进一步加快了恢复速度。

这对于具有很多从未更新或很少更新的索引的用例（例如基于时间的数据）特别有用。此用例通常会生成很多索引，如果没有同步的清除标记，它们的恢复将花费很长时间。

要检查分片是否具有标记，请查找由索引统计信息 API 返回的分片统计信息的`commit`部分：

    GET twitter/_stats?filter_path=**.commit&level=shards

`filter_path`用于减少响应的详细程度，但完全是可选的

```json
{
   "indices": {
      "twitter": {
         "shards": {
            "0": [
               {
                 "commit" : {
                   "id" : "3M3zkw2GHMo2Y4h4/KFKCg==",
                   "generation" : 3,
                   "user_data" : {
                     "translog_uuid" : "hnOG3xFcTDeoI_kvvvOdNA",
                     "history_uuid" : "XP7KDJGiS1a2fHYiFL5TXQ",
                     "local_checkpoint" : "-1",
                     "translog_generation" : "2",
                     "max_seq_no" : "-1",
                     "sync_id" : "AVvFY-071siAOuFGEO9P", // sync_id
                     "max_unsafe_auto_id_timestamp" : "-1"
                   },
                   "num_docs" : 0
                 }
               }
            ],
            "1": ...,
            "2": ...,
            "3": ...,
            "4": ...
         }
      }
   }
}
```

#### 同步清除 API(Synced Flush API)

同步清除 API 允许管理员手动启动同步清除。这对于计划中的（滚动）集群重新启动特别有用，在这种情况下，您可以停止索引编制并且不想等待默认的 5 分钟以使空闲索引自动同步清除。

虽然方便，但此 API 有一些警告：

1. 同步清除是一种尽力而为的操作。任何正在进行的索引操作都将导致同步清除在该分片上失败。这意味着某些分片可能会同步清除，而其他分片则不会同步。有关更多信息，请参见下文。
2. 再次清除分片后，`sync_id`标记即被删除。那是因为清除替换了存储标记的低级 Lucene 提交点。事务日志中未提交的操作不会删除标记。在实践中，应该考虑对索引执行任何索引操作，因为删除标记是因为 Elasticsearch 可以随时触发清除。

> <img src="../images/attention.png" alt="attention" width=35 height=35/>
> 在正在进行索引编制时请求同步清除是无害的。空闲的分片将成功，非空闲的分片将失败。成功的所有分片将具有更快的恢复时间。

```json
POST twitter/_flush/synced

POST kimchy,elasticsearch/_flush/synced

POST _flush/synced

POST _all/_flush/synced
```

响应中包含有关成功同步清除了多个分片的详细信息以及有关任何故障的信息。

成功同步清除的响应：

```json
{
  "_shards": {
    "total": 2,
    "successful": 2,
    "failed": 0
  },
  "twitter": {
    "total": 2,
    "successful": 2,
    "failed": 0
  }
}
```

部分清除失败的响应：

```json
// 当同步清除由于并发索引操作而失败时，将显示以上错误。在这种情况下，HTTP状态代码将为409 CONFLICT
{
  "_shards": {
    "total": 4,
    "successful": 2,
    "failed": 2
  },
  "twitter": {
    "total": 4,
    "successful": 2,
    "failed": 2,
    "failures": [
      {
        "shard": 1,
        "reason": "[2] ongoing operations on primary"
      }
    ]
  }
}
```

有时，故障特定于某个分片副本。失败的副本将不具备快速恢复的资格，但成功的副本仍将具有资格。示例如下：

```json
// 当分片副本无法同步清除时，返回的HTTP状态代码将为409 CONFLICT。
{
  "_shards": {
    "total": 4,
    "successful": 1,
    "failed": 1
  },
  "twitter": {
    "total": 4,
    "successful": 3,
    "failed": 1,
    "failures": [
      {
        "shard": 1,
        "reason": "unexpected error",
        "routing": {
          "state": "STARTED",
          "primary": false,
          "node": "SZNr2J_ORxKTLUCydGX4zA",
          "relocating_node": null,
          "shard": 1,
          "index": "twitter"
        }
      }
    ]
  }
}
```

## 刷新(Refresh)

刷新 API 允许显式刷新一个或多个索引，使自上次刷新以来执行的所有操作都可用于搜索。 （接近）实时功能取决于所使用的索引引擎。例如，内部需要刷新才能调用，但默认情况下会定期计划刷新。

```json
POST /twitter/_refresh

POST /kimchy,elasticsearch/_refresh

POST /_refresh

POST /_all/_refresh
```

## 强制合并(Force Merge)

强制合并 API 允许通过 API 强制合并一个或多个索引。合并与 Lucene 索引在每个分片中保留的段数有关。强制合并操作允许通过合并分段来减少分段的数量。

该调用将阻塞，直到合并完成。如果 http 连接丢失，该请求将在后台继续，并且任何新请求都将被阻止，直到之前的强制合并完成为止。

> <img src="../images/attention.png" alt="attention" width=35 height=35/>
> 强制合并`仅应针对只读索引调用`。对读写索引进行强行合并可能会导致生成非常大的段（每个段> 5Gb），并且合并策略绝不会考虑将其再次合并，直到它主要由已删除的文档组成。这会导致很大的段保留在分片中。

```json
POST /twitter/_forcemerge

POST /kimchy,elasticsearch/_forcemerge

POST /_forcemerge
```

**请求参数**

| 参数                 | 描述                                                                                                                                                                                                                             |
| -------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| max_num_segments     | 合并到的段数。要完全合并索引，请将其设置为 1。默认值是简单地检查是否需要执行合并，如果需要，则执行合并。                                                                                                                         |
| only_expunge_deletes | 合并过程应该只删除其中删除的段。在 Lucene 中，不会从段中删除文档，而只是将其标记为已删除。在段的合并过程中，将创建一个没有这些已删除段的新段。默认为 false。请注意，这不会覆盖`index.merge.policy.expunge_deletes_allowed`阈值。 |
| flush                | 制合并后是否应该执行清除。默认为 true。                                                                                                                                                                                          |

```json
POST /kimchy/_forcemerge?only_expunge_deletes=false&max_num_segments=100&flush=true
```
