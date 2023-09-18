# RabbitMQ 笔记

&emsp;&emsp;MQ（Message Queue，消息队列）是在消息传输过程中保存消息的容器，多用于分布式系统之间的通信，以达到异步处理、应用解耦、流量削峰、日志处理等目的。

&emsp;&emsp;RabbitMQ 采用 AMQP（Advanced Message queuing Protocol，高级消息队列协议）。

## 安装配置

```zsh
docker run -d \
    --name=rabbitmq 
    -v rabbitmq-home:/var/lib/rabbitmq \
    -p 15672:15672 \                    # API 端口
    -p 5672:5672 \                      # 管理后台端口
    -e RABBITMQ_DEFAULT_USER=guest \
    -e RABBITMQ_DEFAULT_PASS=guest \
rabbitmq:management
```

## Hello World

&emsp;&emsp;导入以下依赖：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

&emsp;&emsp;进行以下配置：

```yaml
spring:
  rabbitmq:
    host: 127.0.0.1
    port: 5672
    username: guest
    password: guest
```

&emsp;&emsp;配置类：

```java
public class RabbitMQConfig{
    // RabbitMQ 队列主题名称
    public static final string RABBITMQ_DEMO_TOPIC = "rabbitDemoTopic";
    // RabbitMQ 的 Direct 交换机名称
    public static final string RABBITMQ_DEMO_DIRECT_EXCHANGE = "rabbitDemoDirectExchange";
    // RabbitMQ 的 Direct 交换机和队列绑定的名称
    public static final string RABBITMQ_DEMO_DIRECT_ROUTING = "rabbitDemoDirectRouting";
}
```

&emsp;&emsp;生产者定义交换机：

```java
//实现BeanPostProcessor类，使用Bean的生命周期函数
@Component
public class DirectRabbitConfig implements BeanPostProcessor {
    //这是创建交换机和队列用的rabbitAdmin对象
    @Resource
    private RabbitAdmin rabbitAdmin;
    
    //初始化rabbitAdmin对象
    @Bean
    public RabbitAdmin rabbitAdmin(ConnectionFactory connectionFactory) {
        RabbitAdmin rabbitAdmin = new RabbitAdmin(connectionFactory);
        // 只有设置为 true，spring 才会加载 RabbitAdmin 这个类
        rabbitAdmin.setAutoStartup(true);
        return rabbitAdmin;
    }
    
    //实例化bean后，也就是Bean的后置处理器
    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        //创建交换机
        rabbitAdmin.declareExchange(rabbitmqDemoDirectExchange());
        //创建队列
        rabbitAdmin.declareQueue(rabbitmqDemoDirectQueue());
        return null;
    }
}
```

&emsp;&emsp;生产者 Service 实现：

```java
@Service
public class RabbitMQServiceImpl implements RabbitMQService {
    //日期格式化
    private static SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

    @Resource
    private RabbitTemplate rabbitTemplate;

    @Override
    public String sendMsg(String msg) throws Exception {
        try {
            String msgId = UUID.randomUUID().toString().replace("-", "").substring(0, 32);
            String sendTime = sdf.format(new Date());
            Map<String, Object> map = new HashMap<>();
            map.put("msgId", msgId);
            map.put("sendTime", sendTime);
            map.put("msg", msg);
            rabbitTemplate.convertAndSend(RabbitMQConfig.RABBITMQ_DEMO_DIRECT_EXCHANGE, RabbitMQConfig.RABBITMQ_DEMO_DIRECT_ROUTING, map);
            return "ok";
        } catch (Exception e) {
            e.printStackTrace();
            return "error";
        }
    }
}
```

&emsp;&emsp;消费者监视器类：

```java
@Component
//使用queuesToDeclare属性，如果不存在则会创建队列
@RabbitListener(queuesToDeclare = @Queue(RabbitMQConfig.RABBITMQ_DEMO_TOPIC))
public class RabbitDemoConsumer{
    @RabbitHandler
    public void process(Map map){
        system.out.println(map);
    }
}
```

