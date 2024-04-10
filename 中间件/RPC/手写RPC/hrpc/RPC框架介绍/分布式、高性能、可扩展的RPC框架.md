# 这次我设计了一款TPS百万级别的分布式、高性能、可扩展的RPC框架

> ❝
>
> 作者：冰河
>
> 博客地址：https://binghe001.github.io
>
> ❞

[toc]

***\*大家好，我是冰河~~\****

没错，这次冰河又要搞事情了，这次准备下手的是`RPC框架项目`。为什么要对`RPC框架项目`下手呢，因为在如今分布式、微服务乃至云原生不断发展的过程中，RPC作为底层必不可少通信组件，被广泛应用在分布式、微服务和云原生项目中。

## 为啥要开发RPC框架

事情是这样的，在开发这个RPC框架之前，我花费了不少时间算是对Dubbo框架彻底研究透彻了。

冰河在撸透了Dubbo2.x和Dubbo3.x的源码之后，本来想给大家写一个Dubbo源码解析的专栏。为此，我其实私下准备了一个多月：画流程图、分析源码、写测试Demo，自己在看Dubbo源码时，也为Dubbo源码添加了非常详细的注释。这里，就包含Dubbo2.x和Dubbo3.x的源码。

![图片](data:image/svg+xml,%3C%3Fxml version='1.0' encoding='UTF-8'%3F%3E%3Csvg width='1px' height='1px' viewBox='0 0 1 1' version='1.1' xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink'%3E%3Ctitle%3E%3C/title%3E%3Cg stroke='none' stroke-width='1' fill='none' fill-rule='evenodd' fill-opacity='0'%3E%3Cg transform='translate(-249.000000, -126.000000)' fill='%23FFFFFF'%3E%3Crect x='249' y='126' width='1' height='1'%3E%3C/rect%3E%3C/g%3E%3C/g%3E%3C/svg%3E)

当我就这么熬夜肝文一个多月后，突然发现一个问题：Dubbo经过多年不断的迭代开发，它的源码已经非常多了，如果以文章的形式将Dubbo的源码面面俱到的分析到位，那还不知道要写到何年何月去了。当我写文章分析Dubbo的最新版本3.x时，可能写到专栏的中后期Dubbo已经更新到4.x、5.x，设置有可能是6.x、7.x了。

与其这么费劲吧咧的分析源码，还不如从零开始带着大家一起手撸一个能够在实际生产环境使用的、分布式、高性能、可扩展的RPC框架。这样，大家也能够直观的感受到一个能够在实际场景使用的RPC框架是如何一步步开发出来的。

相信大家在学完《RPC手撸专栏》后，自己再去看Dubbo源码的话，就相对来说简单多了。**你说是不是这样的呢？**

## 你能学到什么？

既然是整个专栏的开篇嘛，肯定是要告诉你在这个专栏中能够学习到哪些实用的技术的。这里，我就画一张图来直观的告诉你在《RPC手撸专栏》能够学到哪些技术吧。

![图片](https://mmbiz.qpic.cn/mmbiz_png/2hHcUic5FEwFYiahehNZDPOdqJiaw1GaGicc51x5X6FuiccNcI4qmQ8wSjU093DYdPJgWYicbxT4c6K3x7bHCN6ialBEQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

相信小伙伴们看到《RPC手撸专栏》涉及到的知识点，应该能够了解到咱们这个从零开始的《RPC手撸专栏》还是比较硬核的吧？

另外，咱这RPC项目支持同步调用、异步调用、回调和单向调用。

- 同步调用：

![图片](https://mmbiz.qpic.cn/mmbiz_png/2hHcUic5FEwFYiahehNZDPOdqJiaw1GaGiccKRQgULyl82NccibzvjXHAxxdaOUpAE3iclTw6fBspf3wiazp6aicx7BKWw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

- 异步调用：

![图片](https://mmbiz.qpic.cn/mmbiz_png/2hHcUic5FEwFYiahehNZDPOdqJiaw1GaGiccOLEnZDJcGL8GBkoRCJicFnsRnWRFORevBRbw5Q5R5qaMqJdfnsrultg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

- 回调：

![图片](https://mmbiz.qpic.cn/mmbiz_png/2hHcUic5FEwFYiahehNZDPOdqJiaw1GaGicc0b5HRTzBLNfVfShsy6YibjOiav1OnHtY5lZMwjQsGaicnDINHAAFlkrzw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

- 单向调用

![图片](data:image/svg+xml,%3C%3Fxml version='1.0' encoding='UTF-8'%3F%3E%3Csvg width='1px' height='1px' viewBox='0 0 1 1' version='1.1' xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink'%3E%3Ctitle%3E%3C/title%3E%3Cg stroke='none' stroke-width='1' fill='none' fill-rule='evenodd' fill-opacity='0'%3E%3Cg transform='translate(-249.000000, -126.000000)' fill='%23FFFFFF'%3E%3Crect x='249' y='126' width='1' height='1'%3E%3C/rect%3E%3C/g%3E%3C/g%3E%3C/svg%3E)

对，没错，咱们《RPC手撸专栏》最终实现的RPC框架的定位就是尽量可以在实际环境使用。通过这个专栏的学习，让大家深入了解到能够在实际场景使用的RPC框架是如何一步步开发出来的。

## 代码结构

我将这个`bhrpc项目`定位为可在实际场景使用的、分布式、高性能、可扩展的RPC框架，目前总体上已经开发并完善的功能达到60+个子项目，大家看图吧（可以点开看大图）。

![图片](https://mmbiz.qpic.cn/mmbiz_png/2hHcUic5FEwFYiahehNZDPOdqJiaw1GaGiccGyUNMzEPaokuWicVN3IxLnVv3ZJTrkLOCHfYUwvHibIskDAz8gKzqS6g/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)



![图片](https://mmbiz.qpic.cn/mmbiz_png/2hHcUic5FEwFYiahehNZDPOdqJiaw1GaGiccAuVGmwaodcKf53ol2BaPhAr1G5iaMgJflAzBNE2D1KhkZicmXhwu7V7A/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

**项目大量使用了对标Dubbo的自定义SPI技术实现高度可扩展性，各位小伙伴可以根据自己的需要，按照SPI的设计要求添加自己实现的自定义插件。**

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/2hHcUic5FEwFYiahehNZDPOdqJiaw1GaGiccADa6oZCkGticofQRibKicdVtYgRbXrxVRD7o6CCKooFXaphibBZhpdlxcQ/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

## 显示效果

说了那么多，咱们一起来看看这个RPC框架的使用效果吧，因为咱们这个RPC框架支持的调用方式有：原生RPC调用、整合Spring(XML/注解)、整合SpringBoot、整合SpringCloud、整合SpringCloud Alibaba，整合Docker和整合K8S七种使用方式。

这里，咱们就以 **整合Spring注解的方式** 来给大家演示下这个RPC框架。

### RPC核心注解说明

为了让大家更好的了解这个RPC框架，我先给大家看下RPC框架的两个核心注解，一个是RPC的服务提供者注解`@RpcService`，一个是RPC的服务调用者注解`@RpcReference`。

（1）服务提供者注解`@RpcService`的核心源码如下所示。

```
/**
 * @author binghe
 * @version 1.0.0
 * @description bhrpc服务提供者注解
 */
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Component
public @interface RpcService {

    /**
     * 接口的Class
     */
    Class<?> interfaceClass() default void.class;

    /**
     * 接口的ClassName
     */
    String interfaceClassName() default "";

    /**
     * 版本号
     */
    String version() default "1.0.0";

    /**
     * 服务分组，默认为空
     */
    String group() default "";

    /**
     * 延迟发布，预留
     */
    int delay() default 0;

    /**
     * 是否导出rpc服务，预留
     */
    boolean export() default true;
}
```

（2）服务调用者注解`@RpcReference`的核心源码如下所示。

```
/**
 * @author binghe
 * @version 1.0.0
 * @description bhrpc服务消费者
 */
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@Autowired
public @interface RpcReference {

    /**
     * 版本号
     */
    String version() default "1.0.0";

    /**
     * 注册中心类型, 目前的类型包含：zookeeper、nacos、etcd、consul
     */
    String registryType() default "zookeeper";

    /**
     * 注册地址
     */
    String registryAddress() default "127.0.0.1:2181";

    /**
     * 负载均衡类型，默认基于ZK的一致性Hash
     */
    String loadBalanceType() default "zkconsistenthash";

    /**
     * 序列化类型，目前的类型包含：protostuff、kryo、json、jdk、hessian2、fst
     */
    String serializationType() default "protostuff";

    /**
     * 超时时间，默认5s
     */
    long timeout() default 5000;

    /**
     * 是否异步执行
     */
    boolean async() default false;

    /**
     * 是否单向调用
     */
    boolean oneway() default false;

    /**
     * 代理的类型，jdk：jdk代理， javassist: javassist代理, cglib: cglib代理
     */
    String proxy() default "jdk";

    /**
     * 服务分组，默认为空
     */
    String group() default "";
}
```

这里，我只列出了服务提供者注解`@RpcService`和服务调用者注解`@RpcReference`的部分源码，后续在RPC框架不断完善的过程中，大家就可以慢慢看到源码的全貌和其每个注解实现的功能。这里，我就不详细介绍了。

**当然啦，在这个RPC框架实现的原生调用方式中，可以不用这些注解就能够实现远程调用。**

### 效果演示

#### 接口定义

定义两个接口，分别为HelloService和HelloPersonService，源码如下所示。

- HelloService接口源码

```
public interface HelloService {
    String hello(String name);
    String hello(Person person);
}
```

- HelloPersonService接口源码

```
public interface HelloPersonService {
    List<Person> getTestPerson(String name,int num);
}
```

#### 实现服务提供者demo

（1）创建HelloService接口和HelloPersonService接口的实现类HelloServiceImpl和HelloPersonServiceImpl，如下所示。

- HelloServiceImpl类源码

```
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
```

可以看到，在HelloServiceImpl类上添加了RPC服务提供者注解`@RpcService`，表示将其发布为一个RPC服务。

- HelloPersonServiceImpl类源码

```
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
```

可以看到，在HelloPersonServiceImpl类上添加了RPC服务提供者注解`@RpcService`，表示将其发布为一个RPC服务。

（2）创建服务提供者demo的配置类ServerConfig，在ServerConfig类中注入RegistryService注册中心接口的实现类，以及RPC服务提供者的核心类RpcServer，如下所示。

```
/**
 * @author binghe
 * @version 1.0.0
 * @description 基于注解的配置类
 */
@Configuration
@ComponentScan(value = {"io.binghe.rpc.demo"})
@PropertySource(value = {"classpath:rpc.properties"})
public class SpringAnnotationProviderConfig {

    @Value("${registry.address}")
    private String registryAddress;

    @Value("${registry.type}")
    private String registryType;

    @Value("${registry.loadbalance.type}")
    private String registryLoadbalanceType;

    @Value("${server.address}")
    private String serverAddress;

    @Value("${reflect.type}")
    private String reflectType;

    @Bean
    public RpcSpringServer rpcSpringServer(){
        return new RpcSpringServer(serverAddress, registryAddress, registryType, registryLoadbalanceType, reflectType);
    }
}
```

（3）创建服务提供者demo的启动类ServerTest，如下所示。

```
/**
 * @author binghe
 * @version 1.0.0
 * @description RPC整合Spring注解，服务提供者demo启动类
 */
public class ServerTest {
    public static void main(String[] args){
        new AnnotationConfigApplicationContext(SpringAnnotationProviderConfig.class);
    }
}
```

#### 实现服务调用者demo

（1）创建测试服务调用者的TestService接口，如下所示。

```
public interface TestService {
    void printResult();
}
```

（2）创建TestService接口的实现类TestServiceImpl，在TestServiceImpl类上标注Spring的`@Service`注解，并在TestServiceImpl类中通过`@RpcReference`注解注入HelloService接口的实现类和HelloPersonService接口的实现类，并实现TestService接口的printResult()方法，源码如下所示。

```
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
```

通过TestServiceImpl类的源码我们可以看到，远程调用HelloService接口的方法时使用的是javassist动态代理，远程调用HelloPersonService接口时，使用的是cglib动态代理。

（3）创建服务调用者demo的配置类ClientConfig，如下所示。

```
@Configuration
@ComponentScan(value = {"io.binghe.rpc.*"})
@PropertySource(value = {"classpath:rpc.properties"})
public class ClientConfig {
}
```

（4）创建服务调用者demo的启动类ClientTest，如下所示。

```
public class ClientTest {

    public static void main(String[] args){
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(ClientConfig.class);
        TestService testService = context.getBean(TestService.class);
        testService.printResult();
        context.close();
    }
}
```

#### 启动服务测试

（1）启动Zookeeper，这里，为了演示简单，就直接在我本机启动单机Zookeeper好了，启动后的效果如下图所示。

![图片](https://mmbiz.qpic.cn/mmbiz_png/2hHcUic5FEwFYiahehNZDPOdqJiaw1GaGicccUDwtgZXHU2umIGtLAD9Z4wg2xAGlXNJN5Lk5aibhH47icAWFgGlcUAg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

（2）启动服务提供者ServerTest类，启动后输出的日志信息如下所示。

```
13:43:36,876  INFO ConnectionStateManager:228 - State change: CONNECTED
13:43:36,905  INFO RpcClient:79 - use cglib dynamic proxy...
13:43:36,942  INFO CuratorFrameworkImpl:235 - Starting
13:43:36,943  INFO ZooKeeper:868 - Initiating client connection, connectString=127.0.0.1:2181 
```

可以看到，服务提供者已经将发布的服务注册到了Zookeeper中。

（3）登录Zookeeper客户端查看Zookeeper中注册的服务，如下所示。

- 查看HelloService接口发布的服务信息

```
[zk: localhost:2181(CONNECTED) 5] get /binghe_rpc/io.binghe.rpc.test.client.HelloService#1.0.0/65eb0d7f-4bf7-4a0a-bafc-1b7e0e030353

{"name":"io.binghe.rpc.test.client.HelloService#1.0.0","id":"65eb0d7f-4bf7-4a0a-bafc-1b7e0e030353","address":"127.0.0.1","port":18866,"sslPort":null,"payload":{"@class":"io.binghe.rpc.center.meta.ServiceMeta","serviceName":"io.binghe.rpc.test.client.HelloService","serviceVersion":"1.0.0","serviceAddr":"127.0.0.1","servicePort":18866},"registrationTimeUTC":1656135817627,"serviceType":"DYNAMIC","uriSpec":null,"enabled":true}
```

- 查看HelloPersonService接口发布的服务信息

```
[zk: localhost:2181(CONNECTED) 7] get /binghe_rpc/io.binghe.rpc.test.client.HelloPersonService#1.0.0/882a5cdb-f581-4a83-8d56-800a8f14e831

{"name":"io.binghe.rpc.test.client.HelloPersonService#1.0.0","id":"882a5cdb-f581-4a83-8d56-800a8f14e831","address":"127.0.0.1","port":18866,"sslPort":null,"payload":{"@class":"io.binghe.rpc.center.meta.ServiceMeta","serviceName":"io.binghe.rpc.test.client.HelloPersonService","serviceVersion":"1.0.0","serviceAddr":"127.0.0.1","servicePort":18866},"registrationTimeUTC":1656135817274,"serviceType":"DYNAMIC","uriSpec":null,"enabled":true}
```

通过Zookeeper客户端可以看出，HelloService接口和HelloPersonService接口发布的服务都已经被注册到Zookeeper了。

（4）启动服务提供者ClientTest类，实现RPC调用，输出的日志信息如下所示。

```
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
```

可以看到，在ClientTest类的命令行输出了远程调用的结果信息。并输出了调用HelloService接口的远程方法使用的是javassist动态代理。调用HelloPersonService接口的远程方法使用的是cglib动态代理。

咱们一起手撸的RPC框架其实还有很多非常强大的功能，这里，就不一一演示了，后面咱们都会一起手撸来实现它。

## 一点点建议

咱们这个专栏属于实战类型比较强的专栏，加上咱们一起从零开始手撸的RPC框架会涉及众多的知识点。

正所谓纸上得来终觉浅，绝知此事要躬行。冰河希望大家在学习这个专栏的时候勤动手，跟着专栏一起实现代码。

期间要多动脑，多总结，这样才能够加深对各项知识点的理解。切忌眼高手低，学了半天却最终啥也没学会。

**好了，今天的文章就到这儿吧，如果文章对你有点帮助，记得给冰河一键三连哦，欢迎将文章转发给更多的小伙伴，冰河将不胜感激~~**

## 一起出发

我会将《RPC手撸专栏》的文章和源码获取方式放到知识星球中，同时在微信上会创建专门的知识星球群，冰河会在知识星球上和星球群里解答球友的提问。

**欢迎大家将文章或者星球转发到群里或者朋友圈，这些内容冰河将用下班、周末、假期的时间不断完善。通过视频+文章+知识小册+直播+作业的形式与你一起学习、提升和进步，最终的目的就是提升你的技术实力，让你在职场走的更远，顺便多赚些钱。**