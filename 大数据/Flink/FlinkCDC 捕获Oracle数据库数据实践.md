# 原料厂数据同步宁钢湖解决方案

[toc]

## 原理说明：

CDC(Change Data Caputure) 变更数据捕获是指通过**监听数据库的运行日志**，例如MySQL的`binlog`,`undolog`,``redolog`等 从而获取数据库相关的数据，包括数据库元数据变更，数据表变更，事务变更等信息。能够做到实时监听数据库进行数据监听，分发，同步等操作。

## 技术选型：

- 原料厂数据库：Oracle 11g
- CDC: FlinkCDC



## 交互负载:

### 交互频率

经过原料厂内部讨论变更数据文件产生的**速率理论上不超过100 QPS**, 即每秒生成的变更数据文件不超过100个 。

### 交互时机

因为采用CDC变更数据捕获技术直接监听数据库日志文件进而产生数据文件, 所以**交互的时机为触发式的**, 当数据库产生变更数据的时候就会有数据文件相应的产生。



## 交互协议：

多方通过讨论采用**文件摆渡**的方式：赛迪方讲数据通过约定好的格式生成文件然后通过类似FTP，SFTP等协议讲文件传输到网闸。一下是我方发送到网闸的数据格式：

### 数据文件命名规则

从原料厂发往网闸的数据文件采用JSON格式，并且是`.json`文件，其命名遵循如下命名规则：`namespace#schema#table#operation#sequence.json` 其中通过井子符号`#`进行连接的部分分别有如下含义：

| **项目**  | **含义**                                                     | **示例**                            |
| :-------: | ------------------------------------------------------------ | ----------------------------------- |
| namespace | 命名空间，标识那个用户，部门，职能方                         | 原料厂(yard)，高炉，烧结(sintering) |
|  schema   | 模式，数据库database等                                       | db_ngyard                           |
|   table   | 模式或数据库下的某张表table                                  | tb_user                             |
| operation | 进行的操作:crud 增删改查等                                   | c                                   |
| sequence  | 文件产生序列号，此序号单调递增，永不重复，采用**雪花算法**进行生成 | 4270186935642112                    |

例如：`ngyard#db_ngyard#fl_transport#c#4270186935642112.json` 表示原料厂db_ngyard数据库下的fl_transport表在在2024-03-05-19:03:25进行新增插入数据而生成的json数据文件。



### 数据文件协议格式

#### Java实体类

数据协议格式对应的Java实体类如下：

```java
public class ChangeData {
    
    // 命名空间，标识那个用户，部门，职能方
    private String namespace;
    
    // 数据源信息:包括数据库,表,连接器,版本等信息
    private Source source;
    
    // 执行的操作：字典[C,R,U,D]:C插入新增数据，R查询数据，U修改更新数据，D删除数据
    private String op;
    
    // 数据变更前的状态：如果是C创建插入新增数据那么为null, 如果为U或D则为修改或删除前的数据(JSON格式)
    private String beforeData;
    
    // 数据变更后的状态：如果是C或U那么此处为插入或更新后的数据(JSON格式)，如果为D那么此处为null
    private String afterData;
    
    // 监听日志文件名称
    private String logfile;
    
    // 监听到日志文件内容的游标位置
    private Long position;
    
    // 触发方式: 枚举[1,2,3]. 其中1表示手动触发, 2表示定时触发, 3表示事件触发
    private Integer triggerType;
    
    // 触发参数: 和触发方式配合使用分别对应以下类型: 1.表示定时触发的定时任务表达式; 2.表示手动进行触发的相关操作,人员等; 3.表示事件触发的具体事件类型-事件名称
    private String triggerParam;
    
    // 事务id
    private String transaction;
    
    // 发生时间戳
    private Long datetime;
}


// 数据源信息
static class Source {
    // 连接器类型
    private String connector;
    // 连接器名称
    private String name;
    // 数据库名称
    private String schema;
    // 模式或数据库下的某张表table
    private String table;
    // 版本信息
    private String version;
}

```



#### JSON数据格式

以上是我方发送方发送的数据在内存序列化为JSON格式之前的Java实体类。一下是序列化之后的JSON对象类型。

```json
{
    // 命名空间，标识那个用户，部门，职能方
    "namespace": "",
    // 数据源信息
    "source": {
    	// 模式，数据库database等
        "schema": "",
        // 模式或数据库下的某张表table
        "table": "",
        // 连接器类型
        "connector": "",
        // 连接器名称
        "name": "",
        // 版本
        "version": ""
    },
    // 执行的操作：字典[C,R,U,D]:C插入新增数据，R查询数据，U修改更新数据，D删除数据
    "op": "",
    // 数据变更前的状态：如果是C创建插入新增数据那么为null, 如果为U或D则为修改或删除前的数据。JSON数据
    "beforeData": "",
    // 数据变更后的状态：如果是C或U那么此处为插入或更新后的数据，如果为D那么此处为null。JSON数据
    "afterData": "",
    // 监听日志文件名称
    "logfile": "",
    // 监听到日志文件内容的游标位置
    "position": 0,
    // 触发方式: 枚举[1,2,3]. 其中1表示手动触发, 2表示定时触发, 3表示事件触发
    "triggerType": 3,
    // 触发参数: 和触发方式配合使用分别对应以下类型: 1.表示定时触发的定时任务表达式; 2.表示手动进行触发的相关操作,人员等; 3.表示事件触发的具体事件类型-事件名称
    "triggerParam": "",
	// 事务id
	"transaction": "",
    // 发生时间戳
    "datetime": 185165458486
}
```

**注意：**其中的`beforeData`和`afterData`都是一个JSON字符串对象，**其对应的JSON格式为目标表table进行JSON序列化之后对应的JSON格式**