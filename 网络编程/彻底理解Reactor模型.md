# 彻底理解Reactor模型

[toc]

## 1.Reactor模式介绍

Reactor模式是事件驱动模型，有一个或多个并发输入源，有一个Service Handler，有多个Request Handlers；这个Service Handler会同步的将输入的请求（Event）多路复用的分发给相应的Request Handler。从结构上，这有点类似生产者消费者模式，即有一个或多个生产者将事件放入一个Queue中，而一个或多个消费者主动的从这个Queue中Poll事件来处理；而Reactor模式则并没有Queue来做缓冲，每当一个Event输入到Service Handler之后，该Service Handler会主动的根据不同的Event类型将其分发给对应的Request Handler来处理。

### **Reactor 类图：**

![img](https://upload-images.jianshu.io/upload_images/4720632-772107bb22dac4a0.png?imageMogr2/auto-orient/strip|imageView2/2/w/527/format/webp)

该图为Reactor模型，实现Reactor模式需要实现以下几个类：

1）EventHandler：事件处理器，可以根据事件的不同状态创建处理不同状态的处理器；

2）Handle：可以理解为事件，在网络编程中就是一个Socket，在数据库操作中就是一个DBConnection；

3）InitiationDispatcher：用于管理EventHandler，分发event的容器，也是一个事件处理调度器，Tomcat的Dispatcher就是一个很好的实现，用于接收到网络请求后进行第一步的任务分发，分发给相应的处理器去异步处理，来保证吞吐量；

4）Demultiplexer：阻塞等待一系列的Handle中的事件到来，如果阻塞等待返回，即表示在返回的Handle中可以不阻塞的执行返回的事件类型。这个模块一般使用操作系统的select来实现。在Java NIO中用Selector来封装，当Selector.select()返回时，可以调用Selector的selectedKeys()方法获取Set<SelectionKey>，一个SelectionKey表达一个有事件发生的Channel以及该Channel上的事件类型。

Reactor模型的核心是：Reactor+Handles。Reactor在一个单独的线程中运行，负责监听和分发事件，将接收到的io事件交给不同的Handle来处理响应。Handles是处理程序执行I/O事件的实际操作，Reactor通过调度适当的Handles来处理io事件。**目前常用的Netty、Redis、Memcached、Nignx都是基于Reactor模式实现的。**



### **Reactor 时序图：**

![img](https://upload-images.jianshu.io/upload_images/4720632-9146b59382d59ebf.png?imageMogr2/auto-orient/strip|imageView2/2/w/546/format/webp)

1）初始化InitiationDispatcher，并初始化一个Handle到EventHandler的Map。

2）注册EventHandler到InitiationDispatcher中，每个EventHandler包含对相应Handle的引用，从而建立Handle到EventHandler的映射（Map）。

3）调用InitiationDispatcher的handle_events()方法以启动Event Loop。在Event Loop中，调用select()方法（Synchronous Event Demultiplexer）阻塞等待Event发生。

4）当某个或某些Handle的Event发生后，select()方法返回，InitiationDispatcher根据返回的Handle找到注册的EventHandler，并回调该EventHandler的handle_events()方法。

5）在EventHandler的handle_events()方法中还可以向InitiationDispatcher中注册新的Eventhandler，比如对AcceptorEventHandler来，当有新的client连接时，它会产生新的EventHandler以处理新的连接，并注册到InitiationDispatcher中。



## 2.Reactor 模型分类

### 一、单Reactor单线程

Reactor线程负责多路分离套接字，accept新连接，并分派请求到handler。[Redis](https://cloud.tencent.com/product/crs?from=10680)使用单Reactor单进程的模型。

原理图：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201114145501775.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjUwMg==,size_16,color_FFFFFF,t_70#pic_center)

消息处理流程：

1. Reactor对象通过select监控连接事件，收到事件后通过dispatch进行转发。
2. 如果是连接建立的事件，则由acceptor接受连接，并创建handler处理后续事件。
3. 如果不是建立连接事件，则Reactor会分发调用Handler来响应。
4. handler会完成read->业务处理->send的完整业务流程。

单Reactor单线程模型只是在代码上进行了组件的区分，但是整体操作还是单线程，不能充分利用硬件资源。handler业务处理部分没有异步。

对于一些小容量应用场景，可以使用单Reactor单线程模型。但是对于高负载、大并发的应用场景却不合适，主要原因如下：

1. 即便Reactor线程的CPU负荷达到100%，也无法满足海量消息的编码、解码、读取和发送。
2. 当Reactor线程负载过重之后，处理速度将变慢，这会导致大量客户端连接超时，超时之后往往会进行重发，这更加重Reactor线程的负载，最终会导致大量消息积压和处理超时，成为系统的性能瓶颈。
3. 一旦Reactor线程意外中断或者进入死循环，会导致整个系统通信模块不可用，不能接收和处理外部消息，造成节点故障。

为了解决这些问题，演进出单Reactor多线程模型。



### 二、单Reactor多线程

原理图：该模型在事件处理器（Handler）部分采用了多线程（线程池）。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201114145540741.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjUwMg==,size_16,color_FFFFFF,t_70#pic_center)

消息处理流程：

1. Reactor对象通过Select监控客户端请求事件，收到事件后通过dispatch进行分发。
2. 如果是建立连接请求事件，则由acceptor通过accept处理连接请求，然后创建一个Handler对象处理连接完成后续的各种事件。
3. 如果不是建立连接事件，则Reactor会分发调用连接对应的Handler来响应。
4. Handler只负责响应事件，不做具体业务处理，通过Read读取数据后，会分发给后面的Worker线程池进行业务处理。
5. Worker线程池会分配独立的线程完成真正的业务处理，如何将响应结果发给Handler进行处理。
6. Handler收到响应结果后通过send将响应结果返回给Client。

相对于第一种模型来说，在处理业务逻辑，也就是获取到IO的读写事件之后，交由线程池来处理，handler收到响应后通过send将响应结果返回给客户端。这样可以降低Reactor的性能开销，从而更专注的做事件分发工作了，提升整个应用的吞吐。

但是这个模型存在的问题：

1. 多线程数据共享和访问比较复杂。如果子线程完成业务处理后，把结果传递给主线程Reactor进行发送，就会涉及共享数据的互斥和保护机制。
2. Reactor承担所有事件的监听和响应，只在主线程中运行，可能会存在性能问题。例如并发百万客户端连接，或者服务端需要对客户端握手进行安全认证，但是认证本身非常损耗性能。

为了解决性能问题，产生了第三种主从Reactor多线程模型。

### 三、主从Reactor多线程

比起第二种模型，它是将Reactor分成两部分：

1. mainReactor负责监听server socket，用来处理网络IO连接建立操作，将建立的socketChannel指定注册给subReactor。
2. subReactor主要做和建立起来的socket做数据交互和事件业务处理操作。通常，subReactor个数上可与CPU个数等同。

Nginx、Swoole、Memcached和Netty都是采用这种实现。

原理图：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201114145717884.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjUwMg==,size_16,color_FFFFFF,t_70#pic_center)

消息处理流程：

1. 从主线程池中随机选择一个Reactor线程作为acceptor线程，用于绑定监听端口，接收客户端连接
2. acceptor线程接收客户端连接请求之后创建新的SocketChannel，将其注册到主线程池的其它Reactor线程上，由其负责接入认证、IP黑白名单过滤、握手等操作
3. 步骤2完成之后，业务层的链路正式建立，将SocketChannel从主线程池的Reactor线程的多路复用器上摘除，重新注册到Sub线程池的线程上，并创建一个Handler用于处理各种连接事件
4. 当有新的事件发生时，SubReactor会调用连接对应的Handler进行响应
5. Handler通过Read读取数据后，会分发给后面的Worker线程池进行业务处理
6. Worker线程池会分配独立的线程完成真正的业务处理，如何将响应结果发给Handler进行处理
7. Handler收到响应结果后通过Send将响应结果返回给Client

**优点：**

1. 父线程与子线程的数据交互简单职责明确，父线程只需要接收新连接，子线程完成后续的业务处理。
2. 父线程与子线程的数据交互简单，Reactor 主线程只需要把新连接传给子线程，子线程无需返回数据。

**缺点：**

编程复杂度较高。

应用场景：这种模型在许多项目中广泛使用，包括 Nginx 主从 Reactor 多进程模型，Memcached 主从多线程，Netty 主从多线程模型的支持。



## 3.Reactor优缺点

Reactor模式的核心是解决多请求问题，如果有特别多的请求同时发生，不会因为线程池被短时间占满而拒绝服务。我们一般实现多请求的模块，会采用线程池的实现方案，这种方案对于并发量不是特别大的场景是足够用的，一般来说单机tps1000以下都是够用的，但是线程池方案的最大缺点就是，如果瞬间有大并发则会一下子耗满线程，整个服务陷入阻塞中，后续请求将无法接入。基于Reactor模式实现的方案，会有一个Dispatcher先接收event，然后快速分发给相应的耗时eventHandler处理器去处理，这样就不会阻塞请求的接收。

Reactor模式和生产者消费者模式最大的区别在于，生产者消费者模式是基于队列的实现，能够解决生产端和消费端处理速度不同步的问题，queue可以基于Java Queue或者基于现有的MQ产品来实现；而Reactor模式是基于事件驱动模型，当接收到请求后会将请求封装成事件，并将事件分发给相应处理事件的Handler，handler处理完成后将事件状态修改为下一个状态，再由Reactor将事件分发给能够处理下一个状态的handler进行处理。两个模式解决的问题都是类似的，只是实现起来有所区别，这是我个人的理解。

Reactor模型具有如下的**优点**：

1. 响应快，不必为单个同步时间所阻塞，虽然Reactor本身依然是同步的；
2. 编程相对简单，可以最大程度的避免复杂的多线程及同步问题，并且避免了多线程/进程的切换开销；
3. 可扩展性，可以方便地通过增加Reactor实例个数来充分利用CPU资源；
4. 可复用性，Reactor模型本身与具体事件处理逻辑无关，具有很高的复用性。

介绍了那么多，Reactor模式的优点很明显，解耦、提升复用性、模块化、可移植性、事件驱动、细力度的并发控制等。Reactor模式的缺点也很明显，模型复杂，因为涉及到内部回调，多线程处理，不容易调试；需要操作系统底层支持，这就导致不同操作系统可能会产生不一样的结果。所以总的来说如果并发要求不是那么高，使用传统的阻塞线程池模型足够了，而且调试、查问题都会简单很多；如果我们的使用场景是会产生瞬时大并发，可以使用Reactor模式来实现，目前大部分的NIO框架或者容器都是实现了Reactor模式，Tomcat、Jetty的NIO都是实现了Reactor模式，Netty和Mina是两套NIO的框架，也分别对Java NIO进行了二次封装实现了Reactor模式。



## 4.Reactor模式实现

> 尊重原著，转载出：[http://www.blogjava.net/DLevin/archive/2015/09/02/427045.html](https://link.jianshu.com/?t=http%3A%2F%2Fwww.blogjava.net%2FDLevin%2Farchive%2F2015%2F09%2F02%2F427045.html)

在[Reactor An Object Behavioral Pattern for Demultiplexing and Dispatching Handles for Synchronous Events](https://link.jianshu.com/?t=http%3A%2F%2Fwww.dre.vanderbilt.edu%2F~schmidt%2FPDF%2Freactor-siemens.pdf)中，一直以`Logging Server`来分析Reactor模式，这个`Logging Server`的实现完全遵循这里对Reactor描述，因而放在这里以做参考。`Logging Server`中的Reactor模式实现分两个部分：Client连接到`Logging Server`和Client向`Logging Server`写Log。因而对它的描述分成这两个步骤。
Client连接到Logging Server

![img](https://upload-images.jianshu.io/upload_images/2100026-c5a784f6750f2c1d.png?imageMogr2/auto-orient/strip|imageView2/2/w/822/format/webp)



1. `Loging Server`注册`LoggingAcceptor`到`InitiationDispatcher`。

2. `Logging Server`调用InitiationDispatcher的handle_events()方法启动。

3. `InitiationDispatcher`内部调用`select()`方法（`Synchronous Event Demultiplexer`），阻塞等待`Client`连接。

4. `Client`连接到`Logging Server`。

5. `InitiationDisptcher`中的`select()`方法返回，并通知`LoggingAcceptor`有新的连接到来。

6. `LoggingAcceptor`调用`accept`方法`accept`这个新连接。

7. `LoggingAcceptor`创建新的`LoggingHandler`。

8. 新的`LoggingHandler`注册到`InitiationDispatcher`中(同时也注册到`Synchonous Event Demultiplexer`中)，等待`Client`发起写log请求。Client向Logging Server写Log

   ![img](https://upload-images.jianshu.io/upload_images/2100026-ca15045fba5b7113.png?imageMogr2/auto-orient/strip|imageView2/2/w/826/format/webp)

   

9. `Client`发送log到`Logging server`。

10. `InitiationDispatcher`监测到相应的`Handle`中有事件发生，返回阻塞等待，根据返回的`Handle`找到`LoggingHandler`，并回调`LoggingHandler`中的`handle_event()`方法。

11. `LoggingHandler`中的`handle_event()`方法中读取`Handle`中的log信息。

12. 将接收到的log写入到日志文件、数据库等设备中。
    3.4步骤循环直到当前日志处理完成。

13. 返回到`InitiationDispatcher`等待下一次日志写请求。



