# InfluxDB学习笔记

InfluxDB 是一个时间序列（time series）数据库，用于收集、存储、处理和可视化时间序列数据。时间序列数据是按时间顺序索引的数据点序列。数据点通常由同一来源的连续测量组成，用于跟踪随时间的变化。

> - 官方文档（InfluxDB 2）：https://docs.influxdata.com/influxdb/v2/

## 安装

使用以下命令运行一个 InfluxDB 的 Docker 容器：

```bash
docker run \
    --name influxdb2 \
    -p 8086:8086 \
    -v INFLUXDB_DATA:/var/lib/influxdb2 \
    -v INFLUXDB_CONF:/etc/influxdb2 \
    influxdb:2
```

在`http://127.1:8086`使用Web UI进行配置或者使用命令进行配置：

```bash
docker exec influxdb2 influx setup \
    --username $USERNAME \
    --password $PASSWORD \
    --org $ORGANIZATION \
    --bucket $BUCKET \
    --force
```

> 关于 setup 子命令的使用可以参考https://docs.influxdata.com/influxdb/v2/reference/cli/influx/setup/

当然，也可以在创建容器的时候完成初始化配置：

```bash
docker run -d -p 8086:8086 \
    -v INFLUXDB_DATA:/var/lib/influxdb2 \
    -v INFLUXDB_CONF:/etc/influxdb2 \
    -e DOCKER_INFLUXDB_INIT_MODE=setup \
    -e DOCKER_INFLUXDB_INIT_USERNAME=<USERNAME> \
    -e DOCKER_INFLUXDB_INIT_PASSWORD=<PASSWORD> \
    -e DOCKER_INFLUXDB_INIT_ORG=<ORG_NAME> \
    -e DOCKER_INFLUXDB_INIT_BUCKET=<BUCKET_NAME> \
    --name influxdb2 \
    influxdb:2
```

## 基本概念

InfluxDB 数据模型将时间序列数据组织到**存储桶**（Bucket）和**测量**（Measurement）中。一个桶可以包含多个测量值。测量包含多个**标签**（Tag）和**字段**（Field）。

- **Bucket**：存储时间序列数据的命名位置。一个桶可以包含多个测量值。
    - **Measurement**：时间序列数据的逻辑分组。给定测量中的所有点都应具有相同的标签。一个测量包含多个标签和字段。
        - **Tag**：值不同但不经常更改的键值对。标签用于存储每个点的元数据。例如，用于识别数据源（如主机、位置、站点等）的东西。
        - **Field**：具有随时间变化的值的键值对，例如：温度、压力、股票价格等。
        - **Timestamp**：与数据关联的时间戳。当存储在磁盘上并查询时，所有数据都按时间排序。

由测量值、标签键、标签值、字段键和时间戳标识的单个数据记录称为一个**数据点**（Point），具有相同测量值、标签键和标签值的一组点组成一个**序列**（Series）。
