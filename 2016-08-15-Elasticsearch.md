# 入门

-------------------------------------------------------------------------------

## 安装测试

[Elasticsearch 权威指南](https://www.gitbook.com/book/looly/elasticsearch-the-definitive-guide-cn)


安装： 直接去下载 http://www.elasticsearch.org/download/

运行：./elasticsearch

检查：`curl 'http://localhost:9200/?pretty'`

```
{
  "name" : "Dakimh the Enchanter",
  "cluster_name" : "elasticsearch",
  "version" : {
    "number" : "2.3.5",
    "build_hash" : "90f439ff60a3c0f497f91663701e64ccd01edbb4",
    "build_timestamp" : "2016-07-27T10:36:52Z",
    "build_snapshot" : false,
    "lucene_version" : "5.5.0"
  },
  "tagline" : "You Know, for Search"
}
```

这样就安装好了。


## 安装marvel


Marvel是Elasticsearch的管理和监控工具，在开发环境下免费使用。它包含了一个叫做Sense的交互式控制台，使用户方便的通过浏览器直接与Elasticsearch进行交互。

```
bin/plugin install license
bin/plugin install marvel-agent
```

如果想要禁用监控：

```
echo 'marvel.agent.enabled: false' >> ./config/elasticsearch.yml
```

打开监控控制台：


## 基本交互

```
curl -i -XGET 'http://localhost:9200/_count?pretty' -d '
{
    "query": {
        "match_all": {}
    }
}
'
```

和关系型数据库的对比术语：

+ Relational DB -> Databases -> Tables -> Rows -> Columns
+ Elasticsearch -> Indices   -> Types  -> Documents -> Fields

创建一个雇员。

```
curl -i -XPUT 'http://localhost:9200/megacorp/employee/1' -d '
{
    "first_name" : "John",
    "last_name" :  "Smith",
    "age" :        25,
    "about" :      "I love to go rock climbing",
    "interests": [ "sports", "music" ]
}
'

# 返回

{"_index":"megacorp","_type":"employee","_id":"1","_version":2,"_shards":{"total":2,"successful":1,"failed":0},"created":false}
```

|名字|	说明|
|---|---|
|megacorp	|索引名-db名|
|employee|	类型名-表名|
|1|	这个员工的ID|

多插入几个文档

```
curl -i -XPUT 'http://localhost:9200/megacorp/employee/2' -d '
{
    "first_name" :  "Jane",
    "last_name" :   "Smith",
    "age" :         32,
    "about" :       "I like to collect rock albums",
    "interests":  [ "music" ]
}
'

curl -i -XPUT 'http://localhost:9200/megacorp/employee/3' -d '
{
    "first_name" :  "Douglas",
    "last_name" :   "Fir",
    "age" :         35,
    "about":        "I like to build cabinets",
    "interests":  [ "forestry" ]
}
'

```

## 搜索

单个搜索

```
curl -i -XGET 'http://localhost:9200/megacorp/employee/2'
```

搜索全部

```
curl -i -XGET 'http://localhost:9200/megacorp/employee/_search'
```

指定查询参数

```
curl -i -XGET 'http://localhost:9200/megacorp/employee/_search?q=last_name:Smith'
```

使用DSL查询 可以指定更复杂的结构

```

curl -i -XGET 'http://localhost:9200/megacorp/employee/_search' -d '
{
    "query" : {
        "match" : {
            "last_name" : "Smith"
        }
    }
}
'

curl -i -XGET 'http://localhost:9200/megacorp/employee/_search' -d '
{
    "query" : {
        "filtered" : {
            "filter" : {
                "range" : {
                    "age" : { "gt" : 30 } 
                }
            },
        "query" : {
                "match" : {
                    "last_name" : "smith"
                }
            }
        }
    }
}
'

```

全文搜索

```
curl -i -XGET 'http://localhost:9200/megacorp/employee/_search' -d '
{
    "query" : {
        "match" : {
            "about" : "rock climbing"
        }
    }
}
'
```

匹配了`rock`和`climbing`的返回。

但是为什么Jane Smith也会出现在结果里呢？原因是“rock”在她的abuot字段中被提及了。因为只有“rock”被提及而“climbing”没有，所以她的_score要低于John。

短语搜索

目前我们可以在字段中搜索单独的一个词，这挺好的，但是有时候你想要确切的匹配若干个单词或者短语(phrases)。例如我们想要查询同时包含"rock"和"climbing"（并且是相邻的）的员工记录。

```
curl -i -XGET 'http://localhost:9200/megacorp/employee/_search' -d '
{
    "query" : {
        "match_phrase" : {
            "about" : "rock climbing"
        }
    }
}
'
```


高亮我们的搜索: 增加highlight参数


```
curl -i -XGET 'http://localhost:9200/megacorp/employee/_search' -d '
{
    "query" : {
        "match_phrase" : {
            "about" : "rock climbing"
        }
    },
     
    "highlight": {
        "fields" : {
            "about" : {}
        }
    }
}
'
```

## 聚合

聚合允许你在数据上生成复杂的分析统计

```
curl -i -XGET 'http://localhost:9200/megacorp/employee/_search' -d '
{
   "aggs": {
    "all_interests": {
      "terms": { "field": "interests" }
    }
  }
}
'


# 可以搭配上查询语句

curl -i -XGET 'http://localhost:9200/megacorp/employee/_search' -d '
{
    "query": {
       "match": {
          "last_name": "smith"
     }
    },

   "aggs": {
    "all_interests": {
      "terms": { "field": "interests" }
    }
  }
}
'
```


聚合也允许分级汇总。例如，让我们统计每种兴趣下职员的平均年龄：

```

curl -i -XGET 'http://localhost:9200/megacorp/employee/_search' -d '
{
    "aggs" : {
        "all_interests" : {
            "terms" : { "field" : "interests" },
            "aggs" : {
                "avg_age" : {
                    "avg" : { "field" : "age" }
                }
            }
        }
    }
}
'
    
```

    
# 分布式集群

-------------------------------------------------------------------------------
## 空集群

## 集群健康

```
curl -i -XGET 'http://localhost:9200/_cluster/health'

{
  "cluster_name": "elasticsearch",
  "status": "yellow",
  "timed_out": false,
  "number_of_nodes": 1,
  "number_of_data_nodes": 1,
  "active_primary_shards": 7,
  "active_shards": 7,
  "relocating_shards": 0,
  "initializing_shards": 0,
  "unassigned_shards": 7,
  "delayed_unassigned_shards": 0,
  "number_of_pending_tasks": 0,
  "number_of_in_flight_fetch": 0,
  "task_max_waiting_in_queue_millis": 0,
  "active_shards_percent_as_number": 50
}

```
status字段提供一个综合的指标来表示集群的的服务状况。三种颜色各自的含义：

+ green: 所有主要分片和复制分片都可用 
+ yellow: 所有主要分片可用，但不是所有复制分片都可用
+ red:	不是所有的主要分片都可用

## 添加索引

+ 索引: 和以前的mysql的库类似。
+ 分片：一个完整的Lucene实例。

默认情况下，一个索引被分配5个主分片，这里为了演示，只分配3个主分片和一个复制分片。

```
curl -i -XPUT 'http://localhost:9200/blogs' -d '
{
   "settings" : {
      "number_of_shards" : 3,
      "number_of_replicas" : 1
   }
}
'

```


