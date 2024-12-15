#  ElasticSearch 笔记

&emsp;&emsp;ElasticSearch（ES）是一个开源的搜索引擎，基于 Lucene 开发。

&emsp;&emsp;ES 与 Kibana、Logstash、Beats 共同组成 Elastic Stack（ELK），广泛用于日志数据分析、实时监控等领域。

&emsp;&emsp;ElasticSearch 的特点是其“倒排索引”技术，将要搜索的字段进行分词，储存与不同词条相关的文档信息。

- 文档：以 JSON 存储的一条信息，例如数据库中的一条记录
- 索引：相同类型文档的集合

## 安装配置

```zsh
# 创建 Docker 网络
docker network create es-net
# 拉取 ES 镜像
docker pull docker.elastic.co/elasticsearch/elasticsearch:7.17.6
# 拉取 Kibana 镜像
docker pull docker.elastic.co/kibana/kibana:7.17.6
# 运行 ES
docker run -d \
    --name es \
    -e "ES_JAVA_OPTS=-Xms512m -Xmx8g" \	# JVM 堆大小
    -e "discovery.type=single-node" \   # 单点（single-node）模式运行
    -v es-data:/usr/share/elasticsearch/data \          # ES 数据
    -v es-plugins:/usr/share/elasticsearch/plugins \    # ES 插件
    -v es-logs:/usr/share/elasticsearch/logs \          # ES 日志
    -v es-conf:/usr/share/elasticsearch/config \        # ES 配置
    --privileged \
    --network es-net \
    -p 9200:9200 \                                      # HTTP API Port
    -p 9300:9300 \                                      # Node Communication Port
docker.elastic.co/elasticsearch/elasticsearch:7.17.6
# 运行 Kibana
docker run -d \
    --name kibana \
    -e "ELASTICSEARCH_HOSTS=http://es:9200" \
    --network es-net \
    -p 5601:5601 \
docker.elastic.co/kibana/kibana:7.17.6
```

&emsp;&emsp;为了建立倒排索引，需要对文档进行分词，其 DSL 为：

```http
POST /_analyze
{
  "analyzer": "standard",
  "text": "I'm a cute cat，我是猫猫"
}
```

&emsp;&emsp;很明显其对中文的效果不佳。因此我们需要安装中文分词器（Tokenizer），如 [IK](https://github.com/medcl/elasticsearch-analysis-ik)：

```zsh
docker exec -it es /bin/bash
./bin/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.17.6/elasticsearch-analysis-ik-7.17.6.zip
docker restart es
```

&emsp;&emsp;ik 提供了两种分词器`ik_smart`和`ik_max_word`，如：

```http
POST /_analyze
{
  "analyzer": "ik_smart",
  "text": "我是一只可爱的猫猫"
}
```

&emsp;&emsp; ik 的配置文件位于`/usr/share/elasticsearch/config/analysis-ik/IKAnalyzer.cfg.xml`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE properties SYSTEM "http://java.sun.com/dtd/properties.dtd">
<properties>
        <comment>IK Analyzer 扩展配置</comment>
        <!--用户可以在这里配置自己的扩展字典 -->
        <entry key="ext_dict"></entry>
         <!--用户可以在这里配置自己的扩展停止词字典-->
        <entry key="ext_stopwords"></entry>
        <!--用户可以在这里配置远程扩展字典 -->
        <!-- <entry key="remote_ext_dict">words_location</entry> -->
        <!--用户可以在这里配置远程扩展停止词字典-->
        <!-- <entry key="remote_ext_stopwords">words_location</entry> -->
</properties>
```

## 索引库操作

&emsp;&emsp;&emsp;创建一个索引库的方法如下：

```http
PUT /user_info
{
  "alias": {},
  "settings": {},
  "mappings": {}
}
```

&emsp;&emsp;查看、删除索引库的操作如下：

```http
GET /user_info
```

```http
DELETE /user_info
```

## 文档操作

&emsp;&emsp;ES创建文档的操作示例如下：

```http
PUT <index_name>/_doc/<id>
{
	# doc content
}
```

```http
PUT <index_name>/_create/<id>
{
	# doc content
}
```

```http
POST <index_name>/_create/<id>
{
	# doc content
}
```

&emsp;&emsp;查询、删除指定文档的示例如下：

```http
GET <index_name>/_doc/<id>
```

```http
DELETE <index_name>/_doc/<id>
```

&emsp;&emsp;文档的内容分为`source`和`shore`，获取文档时使用`_source`、`_source_include`和`_source_exclude`对获取的source进行过滤

```http
GET <index_name>/_doc/<id>?_source=field1,field2
```

&emsp;&emsp;若要部分修改文档，要使用`_update` API，如：

```http
POST  <index_name>/_update/<id>
{
	"doc": {
		"field1": "value1",
		"field2": "value2 "
	}
}
```

> 0. 文档操作（`_doc`）的参数之一为 `op_type` ，默认为`index`（等同于`_index`），表示创建或更新数据，当指定为create（等同于`_create  `）时仅创建，ID存在则拒绝工作
> 1. `POST <index_name>/_doc`可自动生成ID创建新文档
> 2. 也可使用`_source` API只获取文档的source部分
> 3. 在ES 7.x中，`POST  <index_name>/_update/<id>`可写为`POST  <index_name>/——doc/<id>/_update`，但在ES8.x中该API已弃用 

&emsp;&emsp;若需要批量创建/修改/删除文档，可使用 `_bulk` API：

```http
 POST /_bulk
 {"create": {"_index": "test_bulk","_id": 1}}
 {"name": "foo"}
 {"create": {"_index": "test_bulk","_id": 2}}
 {"name": "bar"}
```

> 0. `_bulk` API的操作法方式包括创建（`create`）、索引（`index`）、更新（`update`）和删除（`delete`）
> 1. `_bulk` API的请求体每一组必须为两行，每一行一个JSON 
> 2. `_bulk` API不具有原子性

## Mapping

&emsp;&emsp;`Mapping` 是ES index中对数据结构的定义，可使用`_mapping` API 查看某index的 mapping 信息，如：

```http
GET  <index_name>/_mapping 
```

&emsp;&emsp;默认情况下，ES会根据插入/更新的文档自动修改mapping的properties，这称为 `dynamic mapping` 但是由于ES不支持隐式类型转换且Mapping不可修改已有的字段，因此自动映射应谨慎使用。

&emsp;&emsp;手动创建 Mapping 的示例如下：

```http
PUT user
{
  "mappings": {
    "properties": {
      "id": {
        "type": "integer",
      },
      "name": {
        "type": "keyword"  
      },
      "description": {
        "type": "text",
        "analyzer": "ik_smart"
        "field": {
          "keyword": {
            "type": "keyword"
          }
        }
      }
    }
  }
}
```

&emsp;&emsp;对于每一个 mapping，有以下几个常用属性：

> - `type`：数据类型，如`byte`、`short`、`integer`、`long`、`float`、`double`、`text`（可分词的文本）、`keyword`（不参与分词的文本）、`boolean`、`date`、`object`等
> - `index`：是否创建倒排索引，默认为`true`
> - `analyzer`：分词器
> - `properties`：子属性
> - `copy_to`：指定一个虚拟字段“包含”当前字段，便于搜索

&emsp;&emsp;由于修改mapping的数据类型等会导致倒排索引失效，Elasticsearch 禁止修改部分属性。但是 可以添加新字段或修改某些属性，如：

```http
PUT /user_info/_mapping
{
  "properties": {
    "age": {
      "type": "integer"
    }
  }
}
```

&emsp;&emsp;由于索引的不可变性，要修改索引只能通过reindex实现：

```http
POST _reindex
{
    "source": {
        "index": "src_idx"
    },
    "dest": {
        "index": "desc_idx "
    }
}
```

> 0. 可以使用`GET  <index_name>/_mapping/field/<field_name>`查看指定字段的mapping信息
> 1. 实际上，reindex的工作是将source的文档复制到desc中，当source为空时无任何操作

## 分词器 

&emsp;&emsp;文本分析器（Text Analyzer）用于将可分词的数据按照预先制定的分词规则分为更小粒度的词项（term）。分词器在 写入/修改文档建立索引（Index Time）和搜索（Search Time ）时工作。  

&emsp;&emsp;分词器包括切词器（Tokenizer）、词项过滤器（Token Filter）和 字符过滤器（Character Filter），而分词的步骤包括切词（word segmentation）、归一化（normalization）、去重（distinct）和字典序（sort）。 

&emsp;&emsp;Token Filter在切词后对词项进行处理，而Character Filter则在切词前对字符进行处理。

&emsp;&emsp;在自定义分词器时，必须指定一个Tokenizer，而Token Filter和Character Filter的可以有0个或多个。 

&emsp;&emsp;归一化（Normalization）指对原始文档进行 大小写统一、同义词替换、删除停用等操作来达到提高被匹配率和提高搜索效率的目的。Normalizer只包含Token Filter和Character Filter，用于不能被分词 的 keyword

> 0. 分词器仅加工并返回数据，不会修改原始数据  
