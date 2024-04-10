# 第1章：开篇，从零开始手撸一个能在实际场景使用的高性能RPC框架

[toc]

> 沉淀，成长，突破，帮助他人，成就自我。

**大家好，我是冰河~~**

Dubbo作为在分布式和微服务领域中，最为杰出的RPC服务治理框架之一，撸透它，不仅能够提升自己的网络编程功底，还能进一步提高自身的架构设计能力。在Dubbo中，不仅可以学习到如何实现远程过程调用，还能在Dubbo的源码中学习到SPI扩展技术，序列化与反序列化、动态代理、服务注册与发现、字节码编程等等一系列的高级功能。

## [#](https://binghe.gitcode.host/md/middleware/rpc/2022-06-25-《RPC手撸专栏》-第1章-开篇-手撸一个能在实际场景使用的RPC框架.html#一、文章总览)一、文章总览

![rpc-2022-06-25-003](https://binghe.gitcode.host/assets/images/middleware/rpc/rpc-2022-06-25-003.png)

## [#](https://binghe.gitcode.host/md/middleware/rpc/2022-06-25-《RPC手撸专栏》-第1章-开篇-手撸一个能在实际场景使用的RPC框架.html#二、为何转去手撸rpc)二、为何转去手撸RPC？

冰河在撸透了Dubbo2.x和Dubbo3.x的源码之后，本来想给大家写一个Dubbo源码解析的专栏。为此，我其实私下准备了一个多月：画流程图、分析源码、写测试Demo，自己在看Dubbo源码时，也为Dubbo源码添加了非常详细的注释。这里，就包含Dubbo2.x和Dubbo3.x的源码。

![rpc-2022-06-18-001](https://binghe.gitcode.host/assets/images/middleware/rpc/rpc-2022-06-18-001.png)

那既然想要写一个Dubbo源码解析的专栏，那为何又突然想自己手撸一个RPC框架呢？网上的RPC框架那么多，还要重复造轮子吗？

事情是这样的，当我就这么熬夜肝文一个多月后，突然发现一个问题：Dubbo经过多年不断的迭代开发，它的源码已经非常多了，如果以文章的形式将Dubbo的源码面面俱到的分析到位，那还不知道要写到何年何月去了。当我写文章分析Dubbo的最新版本3.x时，可能写到专栏的中后期Dubbo已经更新到4.x、5.x，设置有可能是6.x、7.x了。

与其这么费劲吧咧的分析源码，还不如从零开始带着大家一起手撸一个能够在实际生产环境使用的高性能RPC框架。这样，大家也能够直观的感受到一个能够在实际场景使用的RPC框架是如何一步步开发出来的。

相信大家在学完《RPC手撸专栏》后，自己再去看Dubbo源码的话，就相对来说简单多了。**你说是不是这样的呢？**

## [#](https://binghe.gitcode.host/md/middleware/rpc/2022-06-25-《RPC手撸专栏》-第1章-开篇-手撸一个能在实际场景使用的RPC框架.html#三、你能学到什么)三、你能学到什么？

既然是整个专栏的开篇嘛，肯定是要告诉你在这个专栏中能够学习到哪些实用的技术的。这里，我就画一张图来直观的告诉你在《RPC手撸专栏》能够学到哪些技术吧。

![rpc-2022-06-25](https://binghe.gitcode.host/assets/images/middleware/rpc/rpc-2022-06-25-001.png)

相信小伙伴们看到《RPC手撸专栏》涉及到的知识点，应该能够了解到咱们这个从零开始的《RPC手撸专栏》还是比较硬核的吧？对，没错，咱们《RPC手撸专栏》最终实现的RPC框架的定位就是尽量可以在实际环境使用。通过这个专栏的学习，让大家深入了解到能够在实际场景使用的RPC框架是如何一步步开发出来的。

## [#](https://binghe.gitcode.host/md/middleware/rpc/2022-06-25-《RPC手撸专栏》-第1章-开篇-手撸一个能在实际场景使用的RPC框架.html#四、效果演示)四、效果演示

说了那么多，咱们一起来看看这个RPC框架的使用效果吧，因为咱们这个RPC框架支持的调用方式有：原生RPC调用、整合Spring(XML/注解)、整合SpringBoot、整合SpringCloud、整合SpringCloud Alibaba，整合Docker和整合K8S七种使用方式。

这里，咱们就以 **整合Spring注解的方式** 来给大家演示下这个RPC框架。

### [#](https://binghe.gitcode.host/md/middleware/rpc/2022-06-25-《RPC手撸专栏》-第1章-开篇-手撸一个能在实际场景使用的RPC框架.html#_1-rpc核心注解说明)1.RPC核心注解说明

为了让大家更好的了解这个RPC框架，我先给大家看下RPC框架的两个核心注解，一个是RPC的服务提供者注解`@RpcService`，一个是RPC的服务调用者注解`@RpcReference`。

（1）服务提供者注解`@RpcService`的核心源码如下所示。

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Component
public @interface RpcService {

    /**
     * 接口的Class
     */
    Class<?> interfaceClass();

    /**
     * 版本号
     */
    String version() default "1.0.0";

    /**
     * 服务分组，默认为空
     */
    String group() default "";
}
 
        @冰河: 代码已经复制到剪贴板
    
```

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20

（2）服务调用者注解`@RpcReference`的核心源码如下所示。

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@Autowired
public @interface RpcReference {

    /**
     * 版本号
     */
    String version() default "1.0.0";

    /**
     * 注册中心类型
     */
    String registryCenterType() default "zookeeper";

    /**
     * 注册地址
     */
    String registryAddress() default "127.0.0.1:2181";

    /**
     * 超时时间，默认5s
     */
    long timeout() default 5000;

    /**
     * 超时时间单位，默认毫秒
     */
    TimeUnit timeUnit() default TimeUnit.MILLISECONDS;

    /**
     * 是否异步执行
     */
    boolean isAsync() default false;

    /**
     * 代理的类型，jdk：jdk代理， javassist: javassist代理, cglib: cglib代理
     */
    String proxy() default "jdk";

}
 
        @冰河: 代码已经复制到剪贴板
    
```

这里，我只列出了服务提供者注解`@RpcService`和服务调用者注解`@RpcReference`的部分源码，后续在RPC框架不断完善的过程中，大家就可以慢慢看到源码的全貌和其每个注解实现的功能。这里，我就不详细介绍了。

**当然啦，在这个RPC框架实现的原生调用方式中，可以不用这些注解就能够实现远程调用。**

### [#](https://binghe.gitcode.host/md/middleware/rpc/2022-06-25-《RPC手撸专栏》-第1章-开篇-手撸一个能在实际场景使用的RPC框架.html#_2-效果演示)2.效果演示

#### [#](https://binghe.gitcode.host/md/middleware/rpc/2022-06-25-《RPC手撸专栏》-第1章-开篇-手撸一个能在实际场景使用的RPC框架.html#_2-1-接口定义)2.1 接口定义

定义两个接口，分别为HelloService和HelloPersonService，源码如下所示。

- HelloService接口源码

```java
public interface HelloService {
    String hello(String name);
    String hello(Person person);
}
 
        @冰河: 代码已经复制到剪贴板
    
```

- HelloPersonService接口源码

```java
public interface HelloPersonService {
    List<Person> getTestPerson(String name,int num);
}
 
        @冰河: 代码已经复制到剪贴板
    
```



#### [#](https://binghe.gitcode.host/md/middleware/rpc/2022-06-25-《RPC手撸专栏》-第1章-开篇-手撸一个能在实际场景使用的RPC框架.html#_2-2-实现服务提供者demo)2.2 实现服务提供者demo

（1）创建HelloService接口和HelloPersonService接口的实现类HelloServiceImpl和HelloPersonServiceImpl，如下所示。

- HelloServiceImpl类源码

```java
@RpcService(interfaceClass = HelloService.class, version = "1.0.0")
public class HelloServiceImpl implements HelloService {

    @Override
    public String hello(String name) {
        return "Hello! " + name;
    }

    @Override
    public String hello(Person person) {
        return "Hello! " + person.getFirstName() + " " + person.getLastName();
    }
}
 
        @冰河: 代码已经复制到剪贴板
    
```



可以看到，在HelloServiceImpl类上添加了RPC服务提供者注解`@RpcService`，表示将其发布为一个RPC服务。

- HelloPersonServiceImpl类源码

```java
@RpcService(interfaceClass = HelloPersonService.class, version = "1.0.0")
public class HelloPersonServiceImpl implements HelloPersonService {
    @Override
    public List<Person> getTestPerson(String name, int num) {
        List<Person> persons = new ArrayList<>(num);
        for (int i = 0; i < num; ++i) {
            persons.add(new Person(Integer.toString(i), name));
        }
        return persons;
    }
}
 
        @冰河: 代码已经复制到剪贴板
    
```



可以看到，在HelloPersonServiceImpl类上添加了RPC服务提供者注解`@RpcService`，表示将其发布为一个RPC服务。

（2）创建服务提供者demo的配置类ServerConfig，在ServerConfig类中注入RegistryService注册中心接口的实现类，以及RPC服务提供者的核心类RpcServer，如下所示。

```java
/**
 * @author binghe
 * @version 1.0.0
 * @description RPC整合Spring注解，服务提供者demo配置类
 */
@ComponentScan(value = {"io.binghe.rpc.*"})
@Configuration
@PropertySource(value = {"classpath:rpc.properties"})
public class ServerConfig {

    @Value("${server.address}")
    private String serverAddress;

    @Value("${registry.address}")
    private String registryAddress;

    @Bean
    public RegistryService registryService(){
        try {
            //这里为了演示，就直接new ZookeeperRegistryService了
            return new ZookeeperRegistryService(registryAddress);
        } catch (Exception e) {
            //异常直接输出了
            e.printStackTrace();
        }
        return null;
    }
    @Bean
    public RpcServer rpcServer() throws Exception {
        return new RpcServer(serverAddress, registryService());
    }
}
 
        @冰河: 代码已经复制到剪贴板
    
```



（3）创建服务提供者demo的启动类ServerTest，如下所示。

```java
/**
 * @author binghe
 * @version 1.0.0
 * @description RPC整合Spring注解，服务提供者demo启动类
 */
public class ServerTest {
    public static void main(String[] args){
        new AnnotationConfigApplicationContext(ServerConfig.class);
    }
}
 
        @冰河: 代码已经复制到剪贴板
    
```



#### [#](https://binghe.gitcode.host/md/middleware/rpc/2022-06-25-《RPC手撸专栏》-第1章-开篇-手撸一个能在实际场景使用的RPC框架.html#_2-3-实现服务调用者demo)2.3 实现服务调用者demo

（1）创建测试服务调用者的TestService接口，如下所示。

```java
public interface TestService {
    void printResult();
}
 
        @冰河: 代码已经复制到剪贴板
    
```



（2）创建TestService接口的实现类TestServiceImpl，在TestServiceImpl类上标注Spring的`@Service`注解，并在TestServiceImpl类中通过`@RpcReference`注解注入HelloService接口的实现类和HelloPersonService接口的实现类，并实现TestService接口的printResult()方法，源码如下所示。

```java
/**
 * @author binghe
 * @version 1.0.0
 * @description 测试RPC服务调用者
 */
@Service
public class TestServiceImpl implements TestService {

    @RpcReference(version = "1.0.0", timeout = 3000, proxy = "javassist", isAsync = true)
    private HelloService helloService;
    
    @RpcReference(proxy = "cglib")
    private HelloPersonService helloPersonService;

    @Override
    public void printResult() {
        String result = helloService.hello("binghe");
        System.out.println(result);
        result = helloService.hello(new Person("binghe001", "binghe002"));
        System.out.println(result);
        System.out.println("=================================");
        List<Person> personList = helloPersonService.getTestPerson("binghe", 2);
        personList.stream().forEach(System.out::println);
    }
}
 
        @冰河: 代码已经复制到剪贴板
    
```



通过TestServiceImpl类的源码我们可以看到，远程调用HelloService接口的方法时使用的是javassist动态代理，远程调用HelloPersonService接口时，使用的是cglib动态代理。

（3）创建服务调用者demo的配置类ClientConfig，如下所示。

```java
@Configuration
@ComponentScan(value = {"io.binghe.rpc.*"})
@PropertySource(value = {"classpath:rpc.properties"})
public class ClientConfig {
}
 
        @冰河: 代码已经复制到剪贴板
    
```



（4）创建服务调用者demo的启动类ClientTest，如下所示。

```java
public class ClientTest {

    public static void main(String[] args){
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(ClientConfig.class);
        TestService testService = context.getBean(TestService.class);
        testService.printResult();
        context.close();
    }
}
 
        @冰河: 代码已经复制到剪贴板
    
```



#### [#](https://binghe.gitcode.host/md/middleware/rpc/2022-06-25-《RPC手撸专栏》-第1章-开篇-手撸一个能在实际场景使用的RPC框架.html#_2-4-启动服务测试)2.4 启动服务测试

（1）启动Zookeeper，这里，为了演示简单，就直接在我本机启动单机Zookeeper好了，启动后的效果如下图所示。

![rpc-2022-06-25-002](https://binghe.gitcode.host/assets/images/middleware/rpc/rpc-2022-06-25-002.png)

（2）启动服务提供者ServerTest类，启动后输出的日志信息如下所示。

```bash
13:43:36,869  INFO CuratorFrameworkImpl:235 - Starting
13:43:36,870  INFO ZooKeeper:868 - Initiating client connection, connectString=127.0.0.1:2181 sessionTimeout=60000 watcher=org.apache.curator.ConnectionState@16150369
13:43:36,871  INFO ClientCnxnSocket:237 - jute.maxbuffer value is 4194304 Bytes
13:43:36,871  INFO ClientCnxn:1653 - zookeeper.request.timeout value is 0. feature enabled=
13:43:36,872  INFO ClientCnxn:1112 - Opening socket connection to server activate.navicat.com/127.0.0.1:2181. Will not attempt to authenticate using SASL (unknown error)
13:43:36,872  INFO ClientCnxn:959 - Socket connection established, initiating session, client: /127.0.0.1:64931, server: activate.navicat.com/127.0.0.1:2181
13:43:36,876  INFO ClientCnxn:1394 - Session establishment complete on server activate.navicat.com/127.0.0.1:2181, sessionid = 0x10048e4ff940004, negotiated timeout = 40000
13:43:36,876  INFO ConnectionStateManager:228 - State change: CONNECTED
13:43:36,905  INFO RpcClient:79 - use cglib dynamic proxy...
13:43:36,942  INFO CuratorFrameworkImpl:235 - Starting
13:43:36,943  INFO ZooKeeper:868 - Initiating client connection, connectString=127.0.0.1:2181 sessionTimeout=60000 watcher=org.apache.curator.ConnectionState@7ba8c737
13:43:36,944  INFO ClientCnxnSocket:237 - jute.maxbuffer value is 4194304 Bytes
13:43:36,944  INFO ClientCnxn:1653 - zookeeper.request.timeout value is 0. feature enabled=
13:43:36,946  INFO ClientCnxn:1112 - Opening socket connection to server activate.navicat.com/127.0.0.1:2181. Will not attempt to authenticate using SASL (unknown error)
13:43:36,947  INFO ClientCnxn:959 - Socket connection established, initiating session, client: /127.0.0.1:64934, server: activate.navicat.com/127.0.0.1:2181
13:43:36,963  INFO ClientCnxn:1394 - Session establishment complete on server activate.navicat.com/127.0.0.1:2181, sessionid = 0x10048e4ff940005, negotiated timeout = 40000
13:43:36,964  INFO ConnectionStateManager:228 - State change: CONNECTED
 
        @冰河: 代码已经复制到剪贴板
    
```



可以看到，服务提供者已经将发布的服务注册到了Zookeeper中。

（3）登录Zookeeper客户端查看Zookeeper中注册的服务，如下所示。

- 查看HelloService接口发布的服务信息

```bash
[zk: localhost:2181(CONNECTED) 5] get /binghe_rpc/io.binghe.rpc.test.client.HelloService#1.0.0/65eb0d7f-4bf7-4a0a-bafc-1b7e0e030353

{"name":"io.binghe.rpc.test.client.HelloService#1.0.0","id":"65eb0d7f-4bf7-4a0a-bafc-1b7e0e030353","address":"127.0.0.1","port":18866,"sslPort":null,"payload":{"@class":"io.binghe.rpc.center.meta.ServiceMeta","serviceName":"io.binghe.rpc.test.client.HelloService","serviceVersion":"1.0.0","serviceAddr":"127.0.0.1","servicePort":18866},"registrationTimeUTC":1656135817627,"serviceType":"DYNAMIC","uriSpec":null,"enabled":true}
 
        @冰河: 代码已经复制到剪贴板
    
```



- 查看HelloPersonService接口发布的服务信息

```bash
[zk: localhost:2181(CONNECTED) 7] get /binghe_rpc/io.binghe.rpc.test.client.HelloPersonService#1.0.0/882a5cdb-f581-4a83-8d56-800a8f14e831

{"name":"io.binghe.rpc.test.client.HelloPersonService#1.0.0","id":"882a5cdb-f581-4a83-8d56-800a8f14e831","address":"127.0.0.1","port":18866,"sslPort":null,"payload":{"@class":"io.binghe.rpc.center.meta.ServiceMeta","serviceName":"io.binghe.rpc.test.client.HelloPersonService","serviceVersion":"1.0.0","serviceAddr":"127.0.0.1","servicePort":18866},"registrationTimeUTC":1656135817274,"serviceType":"DYNAMIC","uriSpec":null,"enabled":true}
 
        @冰河: 代码已经复制到剪贴板
    
```



通过Zookeeper客户端可以看出，HelloService接口和HelloPersonService接口发布的服务都已经被注册到Zookeeper了。

（4）启动服务提供者ClientTest类，实现RPC调用，输出的日志信息如下所示。

```bash
13:56:47,391  INFO ConnectionStateManager:228 - State change: CONNECTED
13:56:47,488  INFO RpcClient:76 - use javassist dynamic proxy...
13:56:47,518  INFO ConnectionStateManager:228 - State change: CONNECTED
13:56:47,545  INFO RpcClient:79 - use cglib dynamic proxy...
13:56:48,253  INFO RpcConsumer:85 - connect rpc server 127.0.0.1 on port 18866 success.
Hello! binghe
Hello! binghe001 binghe002
=================================
0 binghe
1 binghe
 
        @冰河: 代码已经复制到剪贴板
    
```



可以看到，在ClientTest类的命令行输出了远程调用的结果信息。并输出了调用HelloService接口的远程方法使用的是javassist动态代理。调用HelloPersonService接口的远程方法使用的是cglib动态代理。

咱们一起手撸的RPC框架其实还有很多非常强大的功能，这里，就不一一演示了，后面咱们都会一起手撸来实现它。

## [#](https://binghe.gitcode.host/md/middleware/rpc/2022-06-25-《RPC手撸专栏》-第1章-开篇-手撸一个能在实际场景使用的RPC框架.html#五、一点点建议)五、一点点建议

咱们这个专栏属于实战类型比较强的专栏，加上咱们一起从零开始手撸的RPC框架会涉及众多的知识点。正所谓纸上得来终觉浅，绝知此事要躬行。冰河希望大家在学习这个专栏的时候勤动手，跟着专栏一起实现代码。期间要多动脑，多总结，这样才能够加深对各项知识点的理解。切忌眼高手低，学了半天却最终啥也没学会。

**好了，今天的开篇文章就到这儿吧，如果文章对你有点帮助，记得给冰河一键三连哦，欢迎将文章转发给更多的小伙伴，冰河将不胜感激~~**