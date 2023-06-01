#  消息队列终极解决方案——Stream

[toc]

在 Redis 5.0 Stream 没出来之前，消息队列的实现方式都有着各自的缺陷，例如：

- 发布订阅模式 PubSub，不能持久化也就无法可靠的保存消息，并且对于离线重连的客户端不能读取历史消息的缺陷；
- 列表实现消息队列的方式不能重复消费，一个消息消费完就会被删除；
- 有序集合消息队列的实现方式不能存储相同 value 的消息，并且不能阻塞读取消息。

并且以上三种方式在实现消息队列时，只能存储单 value 值，也就是如果你要存储一个对象的情况下，必须先序列化成 JSON 字符串，在读取之后还要反序列化成对象才行，这也给用户的使用带来的不便，基于以上问题，Redis 5.0 便推出了 Stream 类型也是此版本最重要的功能，用于完美地实现消息队列，它借鉴了 Kafka 的设计思路，它支持消息的持久化和消息轨迹的消费，支持 ack 确认消息的模式，让消息队列更加的稳定和可靠。

接下来我们先来了解 Stream 自身的一些特性，然后在综合 Stream 的特性，结合 Java 代码完整的实现一个完美的消息队列示例。

### 基础使用

Stream 既然是一个数据类型，那么和其他数据类型相似，它也有一些自己的操作方法，例如：

- xadd 添加消息；
- xlen 查询消息长度；
- xdel 根据消息 ID 删除消息；
- del 删除整个 Stream；
- xrange 读取区间消息
- xread 读取某个消息之后的消息。

具体使用如下所述。

#### **添加消息**

```shell
127.0.0.1:6379> xadd key * name redis age 10
"1580880750844-0" #结果返回的是消息 id
```

其中 `*` 表示使用 Redis 的规则：时间戳 + 序号的方式自动生成 ID，用户也可以自己指定 ID。

相关语法：

```shell
xadd key ID field string [field string ...]
```



#### **查询消息的长度**

```shell
127.0.0.1:6379> xlen key
(integer) 1
```

相关语法：

```shell
xlen key
```



#### **删除消息**

```shell
127.0.0.1:6379> xadd key * name redis
"1580881585129-0" #消息 ID
127.0.0.1:6379> xlen key
(integer) 1
127.0.0.1:6379> xdel key 1580881585129-0 #删除消息，根据 ID
(integer) 1
127.0.0.1:6379> xlen key
(integer) 0

```

相关语法：

```shell
xdel key ID [ID ...]
```

此命令支持删除一条或多条消息，根据消息 ID。



#### **删除整个 Stream**

```shell
127.0.0.1:6379> del key #删除整个 Stream
(integer) 1
127.0.0.1:6379> xlen key
(integer) 0
```

相关语法：

```shell
del key [key ...]
```

此命令支持删除一个或多个 Stream。



#### **查询区间消息**

```shell
127.0.0.1:6379> xrange mq - +
1) 1) "1580882060464-0"
   2) 1) "name"
      2) "redis"
      3) "age"
      4) "10"
2) 1) "1580882071524-0"
   2) 1) "name"
      2) "java"
      3) "age"
      4) "20"
```

其中：`-` 表示第一条消息，`+` 表示最后一条消息。

相关语法：

```sql
xrange key start end [COUNT count]
```



#### **查询某个消息之后的消息**

```shell
127.0.0.1:6379> xread count 1 streams mq 1580882060464-0
1) 1) "mq"
   2) 1) 1) "1580882071524-0"
         2) 1) "name"
            2) "java"
            3) "age"
            4) "20"
```

在名称为 mq 的 Stream 中，从消息 ID 为 1580882060464-0 的，往后查询一条消息。

相关语法：

```css
xread [COUNT count] [BLOCK milliseconds] STREAMS key [key ...] ID [ID ...]
```

此命令提供了阻塞读的参数 block，我们可以使用它读取从当前数据以后新增数据，命令如下：

```shell
127.0.0.1:6379> xread count 1 block 0 streams mq $
```

其中 `block 0` 表示一直阻塞，`$` 表示从最后开始读取，这个时候新开一个命令行插入一条数据，此命令展示的结果如下：

```shell
127.0.0.1:6379> xadd mq * name sql age 20 #新窗口添加数据
"1580890737890-0"
#阻塞读取到的新数据
127.0.0.1:6379> xread count 1 block 0 streams mq $
1) 1) "mq"
   2) 1) 1) "1580890737890-0"
         2) 1) "name"
            2) "sql"
            3) "age"
            4) "20"
(36.37s)
```



### 基础版消息队列

使用 Stream 消费分组实现消息队列的功能和列表方式的消息队列比较相似，使用 xadd 命令和 xread 循环读取就可以实现基础版的消息队列，具体代码如下：

```java
import com.google.gson.Gson;
import redis.clients.jedis.Jedis;
import redis.clients.jedis.StreamEntry;
import redis.clients.jedis.StreamEntryID;
import java.util.AbstractMap;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class StreamExample {
    public static void main(String[] args) throws InterruptedException {
        // 消费者
        new Thread(() -> consumer()).start();
        Thread.sleep(1000);
        // 生产者
        producer();
    }
    /**
     * 生产者
     */
    public static void producer() throws InterruptedException {
        Jedis jedis = new Jedis("127.0.0.1", 6379);
        // 推送消息
        Map<String, String> map = new HashMap<>();
        map.put("name", "redis");
        map.put("age", "10");
        // 添加消息
        StreamEntryID id = jedis.xadd("mq", null, map);
        System.out.println("消息添加成功 ID：" + id);
    }
    /**
     * 消费者
     */
    public static void consumer() {
        Jedis jedis = new Jedis("127.0.0.1", 6379);
        // 消费消息
        while (true) {
            // 获取消息，new StreamEntryID().LAST_ENTRY 标识获取当前时间以后的新增消息
            Map.Entry<String, StreamEntryID> entry = new AbstractMap.SimpleImmutableEntry<>("mq",
                    new StreamEntryID().LAST_ENTRY);
            // 阻塞读取一条消息（最大阻塞时间120s）
            List<Map.Entry<String, List<StreamEntry>>> list = jedis.xread(1, 120 * 1000, entry);
            if (list.size() == 1) {
                // 读取到消息
                System.out.println("读取到消息 ID：" + list.get(0).getValue().get(0).getID());
                // 使用 Gson 来打印 JSON 格式的消息内容
                System.out.println("内容：" + new Gson().toJson(list.get(0).getValue().get(0).getFields()));
            }
        }
    }
}
```

以上代码运行结果如下：

```bash
消息添加成功 ID：1580895735148-0
读取到消息 ID：1580895735148-0
内容：{"name":"redis","age":"10"}
```

以上代码需要特殊说明的是，我们使用 `new StreamEntryID().LAST_ENTRY` 来实现读取当前时间以后新增的消息，如果要从头读取历史消息把这行代码中的 `.LAST_ENTRY` 去掉即可。

还有一点需要注意，在 Jedis 框架中如果使用 jedis.xread() 方法来阻塞读取消息队列，第二个参数 long block 必须设置大于 0，如果设置小于 0，此阻塞条件就无效了，我查看了 jedis 的源码发现，它只有判断在大于 0 的时候才会设置阻塞属性，源码如下：

```java
if (block > 0L) {
    params[streamsIndex++] = Keyword.BLOCK.raw;
    params[streamsIndex++] = Protocol.toByteArray(block);
}
```

所以 block 属性我们可以设置一个比较大的值来阻塞读取消息。

> 所谓的阻塞读取消息指的是当队列中没有数据时会进入休眠模式，等有数据之后才会唤醒继续执行。

### 小结

本文介绍了 Stream 的基础方法，并使用 xadd 存入消息和 xread 循环阻塞读取消息的方式实现了简易版的消息队列，交互流程如下图所示：

![Stream简易版交互图.png](https://learn.lianglianglee.com/%E4%B8%93%E6%A0%8F/Redis%20%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E5%AE%9E%E6%88%98/assets/3e54f6b0-6f6f-11ea-832c-2703165cf4af)

然后这些并不是 Stream 最核心的功能，下文我们将带领读者朋友们，使用消费分组来实现一个完美的消息队列。



## 完美的Stream 消息队列

在开始使用消息分组之前，我们必须手动创建分组才行，以下是几个和 Stream 分组有关的命令，我们先来学习一下它的使用。

### 消息分组命令

#### **创建消费者群组**

```shell
127.0.0.1:6379> xgroup create mq group1 0-0 
OK
```

相关语法：

```vbnet
xgroup create stream-key group-key ID
```

其中：

- mq 为 Stream 的 key；
- group1 为分组的名称；
- 0-0 表示从第一条消息开始读取。

如果要从当前最后一条消息向后读取，使用 `$` 即可，命令如下：

```shell
127.0.0.1:6379> xgroup create mq group2 $
OK
```

#### **读取消息**

```shell
127.0.0.1:6379> xreadgroup group group1 c1 count 1 streams mq >
1) 1) "mq"
   2) 1) 1) "1580959593553-0"
         2) 1) "name"
            2) "redis"
            3) "age"
            4) "10"
```

相关语法：

```vbnet
xreadgroup group group-key consumer-key streams stream-key
```

其中：

- `>` 表示读取下一条消息；
- group1 表示分组名称；
- c1 表示 consumer（消费者）名称。

xreadgroup 命令和 xread 使用类似，也可以设置阻塞读取，命令如下：

```shell
127.0.0.1:6379> xreadgroup group group1 c2 streams mq >
1) 1) "mq"
   2) 1) 1) "1580959606181-0"
         2) 1) "name"
            2) "java"
            3) "age"
            4) "20"
127.0.0.1:6379> xreadgroup group group1 c2 streams mq >
(nil) #队列中的消息已经被读取完
127.0.0.1:6379> xreadgroup group group1 c1 count 1 block 0 streams mq > #阻塞读取
```

此时打开另一个命令行创建使用 xadd 添加一条消息，阻塞命令执行结果如下：

```shell
127.0.0.1:6379> xreadgroup group group1 c1 count 1 block 0 streams mq >
1) 1) "mq"
   2) 1) 1) "1580961475368-0"
         2) 1) "name"
            2) "sql"
            3) "age"
            4) "20"
(86.14s)
```

#### **消息消费确认**

接收到消息之后，我们要手动确认一下（ack），命令如下：

```shell
127.0.0.1:6379> xack mq group1 1580959593553-0
(integer) 1
```

相关语法：

```vbnet
xack key group-key ID [ID ...]
```

消费确认增加了消息的可靠性，一般在业务处理完成之后，需要执行 ack 确认消息已经被消费完成，整个流程的执行如下图所示：

![image.png](https://learn.lianglianglee.com/%E4%B8%93%E6%A0%8F/Redis%20%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E5%AE%9E%E6%88%98/assets/a5dfbd80-6f72-11ea-833b-93fabc8068c9)

#### **查询未确认的消费队列**

```shell
127.0.0.1:6379> xpending mq group1
1) (integer) 1 #未确认（ack）的消息数量为 1 条
2) "1580994063971-0"
3) "1580994063971-0"
4) 1) 1) "c1"
      2) "1"
127.0.0.1:6379> xack  mq group1 1580994063971-0 #消费确认
(integer) 1
127.0.0.1:6379> xpending mq group1
1) (integer) 0 #没有未确认的消息
2) (nil)
3) (nil)
4) (nil)
```

#### **xinfo 查询相关命令**

**1. 查询流信息**

```shell
127.0.0.1:6379> xinfo stream mq
 1) "length"
 2) (integer) 2 #队列中有两个消息
 3) "radix-tree-keys"
 4) (integer) 1
 5) "radix-tree-nodes"
 6) (integer) 2
 7) "groups"
 8) (integer) 1 #一个消费分组
 9) "last-generated-id"
10) "1580959606181-0"
11) "first-entry"
12) 1) "1580959593553-0"
    2) 1) "name"
       2) "redis"
       3) "age"
       4) "10"
13) "last-entry"
14) 1) "1580959606181-0"
    2) 1) "name"
       2) "java"
       3) "age"
       4) "20"
```

相关语法：

```vbnet
xinfo stream stream-key
```

**2. 查询消费组消息**

```shell
127.0.0.1:6379> xinfo groups mq
1) 1) "name"
   2) "group1" #消息分组名称
   3) "consumers"
   4) (integer) 1 #一个消费者客户端
   5) "pending"
   6) (integer) 1 #一个未确认消息
   7) "last-delivered-id"
   8) "1580959593553-0" #读取的最后一条消息 ID
```

相关语法：

```bash
xinfo groups stream-key
```

**3. 查看消费者组成员信息**

```shell
127.0.0.1:6379> xinfo consumers mq group1
1) 1) "name"
   2) "c1" #消费者名称
   3) "pending"
   4) (integer) 0 #未确认消息
   5) "idle"
   6) (integer) 481855
```

相关语法：

```vbnet
xinfo consumers stream group-key
```

#### **删除消费者**

```shell
127.0.0.1:6379> xgroup delconsumer mq group1 c1
(integer) 1
```

相关语法：

```vbnet
xgroup delconsumer stream-key group-key consumer-key
```

#### **删除消费组**

```shell
127.0.0.1:6379> xgroup destroy mq group1
(integer) 1
```

相关语法：

```vbnet
xgroup destroy stream-key group-key
```

### 代码实战

接下来我们使用 Jedis 来实现 Stream 分组消息队列，代码如下：

```java
import com.google.gson.Gson;
import redis.clients.jedis.Jedis;
import redis.clients.jedis.StreamEntry;
import redis.clients.jedis.StreamEntryID;
import utils.JedisUtils;

import java.util.AbstractMap;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class StreamGroupExample {
    private static final String _STREAM_KEY = "mq"; // 流 key
    private static final String _GROUP_NAME = "g1"; // 分组名称
    private static final String _CONSUMER_NAME = "c1"; // 消费者 1 的名称
    private static final String _CONSUMER2_NAME = "c2"; // 消费者 2 的名称
    public static void main(String[] args) {
        // 生产者
        producer();
        // 创建消费组
        createGroup(_STREAM_KEY, _GROUP_NAME);
        // 消费者 1
        new Thread(() -> consumer()).start();
        // 消费者 2
        new Thread(() -> consumer2()).start();
    }
    /**
     * 创建消费分组
     * @param stream    流 key
     * @param groupName 分组名称
     */
    public static void createGroup(String stream, String groupName) {
        Jedis jedis = JedisUtils.getJedis();
        jedis.xgroupCreate(stream, groupName, new StreamEntryID(), true);
    }
    /**
     * 生产者
     */
    public static void producer() {
        Jedis jedis = JedisUtils.getJedis();
        // 添加消息 1
        Map<String, String> map = new HashMap<>();
        map.put("data", "redis");
        StreamEntryID id = jedis.xadd(_STREAM_KEY, null, map);
        System.out.println("消息添加成功 ID：" + id);
        // 添加消息 2
        Map<String, String> map2 = new HashMap<>();
        map2.put("data", "java");
        StreamEntryID id2 = jedis.xadd(_STREAM_KEY, null, map2);
        System.out.println("消息添加成功 ID：" + id2);
    }
    /**
     * 消费者 1
     */
    public static void consumer() {
        Jedis jedis = JedisUtils.getJedis();
        // 消费消息
        while (true) {
            // 读取消息
            Map.Entry<String, StreamEntryID> entry = new AbstractMap.SimpleImmutableEntry<>(_STREAM_KEY,
                    new StreamEntryID().UNRECEIVED_ENTRY);
            // 阻塞读取一条消息（最大阻塞时间120s）
            List<Map.Entry<String, List<StreamEntry>>> list = jedis.xreadGroup(_GROUP_NAME, _CONSUMER_NAME, 1,
                    120 * 1000, true, entry);
            if (list != null && list.size() == 1) {
                // 读取到消息
                Map<String, String> content = list.get(0).getValue().get(0).getFields(); // 消息内容
                System.out.println("Consumer 1 读取到消息 ID：" + list.get(0).getValue().get(0).getID() +
                        " 内容：" + new Gson().toJson(content));
            }
        }
    }
    /**
     * 消费者 2
     */
    public static void consumer2() {
        Jedis jedis = JedisUtils.getJedis();
        // 消费消息
        while (true) {
            // 读取消息
            Map.Entry<String, StreamEntryID> entry = new AbstractMap.SimpleImmutableEntry<>(_STREAM_KEY,
                    new StreamEntryID().UNRECEIVED_ENTRY);
            // 阻塞读取一条消息（最大阻塞时间120s）
            List<Map.Entry<String, List<StreamEntry>>> list = jedis.xreadGroup(_GROUP_NAME, _CONSUMER2_NAME, 1,
                    120 * 1000, true, entry);
            if (list != null && list.size() == 1) {
                // 读取到消息
                Map<String, String> content = list.get(0).getValue().get(0).getFields(); // 消息内容
                System.out.println("Consumer 2 读取到消息 ID：" + list.get(0).getValue().get(0).getID() +
                        " 内容：" + new Gson().toJson(content));
            }
        }
    }
}
```

以上代码运行结果如下：

```bash
消息添加成功 ID：1580971482344-0
消息添加成功 ID：1580971482415-0
Consumer 1 读取到消息 ID：1580971482344-0 内容：{"data":"redis"}
Consumer 2 读取到消息 ID：1580971482415-0 内容：{"data":"java"}
```

其中，jedis.xreadGroup() 方法的第五个参数 noAck 表示是否自动确认消息，如果设置 true 收到消息会自动确认（ack）消息，否则则需要手动确认。

> 注意：Jedis 框架要使用最新版，低版本 block 设置大于 0 时，会有 bug 抛连接超时异常。

可以看出，同一个分组内的多个 consumer 会读取到不同消息，不同的 consumer 不会读取到分组内的同一条消息。

### 小结

本文我们介绍了 Stream 分组的相关知识，使用 Jedis 的 xreadGroup() 方法实现了消息的阻塞读取，并且使用此方法自带 noAck 参数，实现了消息的自动确认，通过本文我们也知道了，一个分组内的多个 consumer 会轮询收到消息队列的消息，并且不会出现一个消息被多个 consumer 读取的情况。

如果你看了本文的知识还是觉得没看懂，那是因为你没有结合实践去理解，所以如果对本文还有疑问，跟着本文一步一步实践起来吧。