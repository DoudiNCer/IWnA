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
docker pull docker.elastic.co/elasticsearch/elasticsearch:7.17.12
# 拉取 Kibana 镜像
docker pull docker.elastic.co/kibana/kibana:7.17.12
# 运行 ES
docker run -d \
    --name es \
    -e "ES_JAVA_OPTS=-Xms4g -Xmx8g" \	# JVM 堆大小
    -e "discovery.type=single-node" \   # 单点（single-node）模式运行
    -v es-data:/usr/share/elasticsearch/data \          # ES 数据
    -v es-plugins:/usr/share/elasticsearch/plugins \    # ES 插件
    -v es-logs:/usr/share/elasticsearch/logs \          # ES 日志
    --privileged \
    --network es-net \
    -p 9200:9200 \                                      # HTTP API Port
    -p 9300:9300 \                                      # Node Communication Port
docker.elastic.co/elasticsearch/elasticsearch:7.17.12
# 运行 Kibana
docker run -d \
    --name kibana \
    -e "ELASTICSEARCH_HOSTS=http://es:9200" \
    --network es-net \
    -p 5601:5601 \
docker.elastic.co/kibana/kibana:7.17.12
```

