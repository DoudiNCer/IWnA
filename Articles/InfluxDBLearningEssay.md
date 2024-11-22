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

## 数据操作

### 数据写入

在InfluxDB中，使用行协议（Line Protocol）进行数据的写入，每一行表示一个数据点。行协议包括Measurement、Tag集、Field集和纳秒级时间戳，其格式如下：

```
measurement,tag1=val1,tag2=val2 field1="v1",field2=1i 0000000000000000000
```

如`cat_info,name=Mimi,sex=0,breed=Ragdoll temperature=38.6,weight=4.5,length=45.0 1721756400000000000`

如果我们要删除数据，我们只能根据时间范围、Measurement和Tag进行删除。

### 数据查询

Flux 是 InfluxDB 的函数式数据脚本语言，用于查询、分析和处理数据。

每个Flux查询语句都至少包含数据源（`from()`）、时间范围（`range()`）和数据过滤器（`filter()`）三部分，使用管道（`|>`）将上一个函数的输出转发到下一个函数的输入，如：

```flux
from(bucket: "cat")
  |> range(start: 2024-07-01T00:00:00.000Z, stop: 2024-07-31T00:00:00.001Z)
  |> filter(fn: (r) => r._measurement == "cat_info")
  |> filter(fn: (r) => r._field == "weight" or r._field == "length" or r._field == "temperature")
```

使用`from()`从指定的 Bucket 读取数据，如：

```flux
from(bucket:"cat")
```

作为一种保护措施，InfluxDB不会查询无时间界限的数据。使用`range()`对获取到的数据的时间范围进行限制，该函数接收两个参数`start`和`stop`，可以使用相对于“现在”的负的相对[持续范围](https://docs.influxdata.com/flux/v0/data-types/basic/duration/)，也可以使用时间戳[绝对值](https://docs.influxdata.com/flux/v0/data-types/basic/time/)。如：

```flux
from(bucket: "cat")
  |> range(start: -7d)
```

和

```flux
from(bucket: "cat")
  |> range(start: 2024-07-01T00:00:00.000Z, stop: 2024-07-31T00:00:00.001Z)
```

使用`filter()`对数据进行过滤，该函数接收一个[谓词函数](https://docs.influxdata.com/flux/v0/get-started/syntax-basics/#predicate-functions)`fn`对数据进行评估，去除评估结果为`false`的数据。比如上面示例中的

```flux
filter(fn: (r) => r._measurement == "cat_info")
```

和

```flux
filter(fn: (r) => r._field == "weight" or r._field == "length" or r._field == "temperature")
```

> 很明显，InfluxDB将每一个Field键值对作为一条独立的数据进行处理

最后使用`yield()`输出查询结果。Flux默认为单条查询自动补充`yield()`，当一个 Flux 包含多个查询时需要使用`yield()`的`name`参数为其命名。

### 数据处理

#### 数据转换

使用`map()`函数对某一列的数据进行转换，如将猫咪的体温`temperature`从摄氏温度转换为华氏温度：

```flux
from(bucket: "cat")
  |> range(start: 2024-07-01T00:00:00.000Z, stop: 2024-07-31T00:00:00.001Z)
  |> filter(fn: (r) => r._measurement == "cat_info")
  |> filter(fn: (r) => r._field == "temperature")
  |> map(fn: (r) => ({r with _value: (r._value * 1.8) + 32.0}))
```

#### 数据分组

默认情况下，`from()`函数将获得的数据按照序列（测量、Tag 和 Field）进行分组，使用带参数的`group()`函数仅对部分条件分组；使用不带参数的`group()`取消自定义分组。

每一张表都有一组键`[_start, _stop, _field, _measurement, host]`，分组的本质是定义输出表的键。

#### 数据聚合

> 详细的聚合功能参考：https://docs.influxdata.com/influxdb/v2/query-data/flux/window-aggregate/

在聚合（Aggregate）数据前，需使用`window()`函数将数据范围多个窗口，`window()`函数接收`every`范围对`_start`到`_stop`的整个范围进行分区。

分区后，可使用聚合函数`mean()`（取平均值）等进行聚合。

由于聚合函数无法确定聚合后数据的时间，`_time`字段会被丢弃，需要使用`duplicate()`函数手动补充时间，比如：

```flux
duplicate(column: "_stop", as: "_time")
```

为了进行可视化等操作，将集合后的数据放入一个无限窗口中：

```flux
window(every: inf)
```

## Flux

Flux 是一门开源的数据脚本语言，用于从包括但不限于时序数据库（InfluxDB）、关系型SQL数据库和CSV中查询、分析和处理数据。

> - Flux 文档：https://docs.influxdata.com/flux/v0/
> - 在未来的InfluxDB v3 中，将放弃对 Flux 的支持而改用 InfluxQL：https://docs.influxdata.com/flux/v0/future-of-flux/

### 数据模型

- **表流**（Stream of Tables）：不少于0个表的集合，是Flux操作的对象。
- **表**（Table）：按组键进行分组的列的集合
- **列**（Column）：相同基本类型值的集合
- **行**（Row）：相关联的列值的集合
- **组键**（Group key）：组键定义使用哪些列对表流中的表进行分组。表流中的每个表都代表一个唯一的组键实例。表中的所有行的每个组键列都包含相同的值。

