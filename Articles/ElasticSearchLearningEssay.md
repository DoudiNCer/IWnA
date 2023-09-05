# ElasticSearch 笔记

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

