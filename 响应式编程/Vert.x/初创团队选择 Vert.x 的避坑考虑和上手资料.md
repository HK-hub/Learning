# 初创团队选择 Vert.x 的避坑考虑和上手资料

[toc]

> https://zhuanlan.zhihu.com/p/33832486

> 2022-05-08更新：加入用Clojure 语言开发Vert.x的方法）
> （2021-03-25更新：最近在搞图数据库，增加了一篇和Neo4j联用的教程）
> （2019-12-04更新：增加 REST 一篇教程）
> （2019-01-02更新：增加采用 Vert.x 和 RxJava 不用 Redis 做数据缓存的做法，进阶阅读
> （2018-06-05更新：增加对Real World demo评述，增加一篇 Clement Escoffier 的教程
> （2018-04-17更新：修正关于 [Vert.x - From zero to (micro)-hero](https://link.zhihu.com/?target=http%3A//escoffier.me/vertx-hol/) 的评述）
> （2018-02-22更新：增加关于多语言选择内容，增加一篇教程）

最近需要做一个可穿戴 IoT 的全栈应用，决定采用关注了很久的 Vert.x 框架。 团队只有不到3人，之前的项目经历多是 C/C++, Clojure 和 Python，所以迁移到 Vert.x 有些学习成本。一方面是对概念和工具不熟悉，另一方面则是希望了解 Vert.x 在项目中的最佳实践，尽量少踩坑。

这篇文章列出一些我们在技术选型时的考虑，以及觉得很有启发的参考项目或教程，希望对于其他迁移型 Vert.x 初学者有所帮助。

## Vert.x 能解决的问题

开源的 Vert.x 其实不是一个框架，而是一个以“异步非阻塞”编程模型为核心思想的工具集。它基本上囊括了开发网络应用时需要用到的所有组件，不管是搭建服务、路由管理、用户权限理、数据库对接、网络通讯、集群管理……都可以用 Vert.x 搞定。

Vert.x 的另一个重要特性是支持7种编程语言，需要糙快猛时可以用脚本语言（如 Ruby/Groovy，可惜暂不支持 Python），需要专注数据处理则可以用函数式语言（如 Scala/Kotlin，后面有望加入 [Haskell](https://link.zhihu.com/?target=https%3A//eta-lang.org/contribute/gsoc/2018%23vertx) 和 [Clojure](https://zhuanlan.zhihu.com/p/33841273)），需要兼顾前后端时可以用 Javascript（最近还推出了基于 GraalVM 的 [es4x](https://link.zhihu.com/?target=https%3A//reactiverse.io/es4x/)）.

![img](https://pic4.zhimg.com/80/v2-738897d759ef68572b15ff40ba678cc7_720w.webp)

支持7种开发语言是 Vert.x 主打的一项特性

而且，Vert.x 允许同一个项目的不同模块用不同语言来开发，也就是 polyglot. 考虑到初创团队成员加入时的自带技能往往并不一致，所以 polyglot 也是一个对初创团队很友好的特性。

截至2022年5月8日， Vert.x 发布了4.2.9 版本，目前Github 星标超过 1.29w，充分显示了这个工具集的健壮程度。值得一提的是，Vert.x 的好几位贡献者来自中国。一项重要功能——对 kotlin-coroutine 的支持——就是出自上海网友 steam1984 之手。

补充一段知乎 Vert.x 著名答主 

[@圆胖肿](https://www.zhihu.com/people/019f06627adc51d9cd030517a6f14c26)

 的评论，Vert.x 应该使用那种语言开发？



> 如果之前熟悉c/c++的，直接上java
> 如果之前熟悉python的，用groovy，groovy跟python很接近，是java化的python
> 如果之前熟悉clojure的，那就用[我们提供的支持](https://zhuanlan.zhihu.com/p/33841273)
> 这样三个人都学点新的东西，就可以通过vert.x整合起来了，具体的整合可以参考专栏之前的文章：[Vert.x的一个常见问题以及多语言制霸环境配置](https://zhuanlan.zhihu.com/p/30913753)

## 初创团队为什么选择 Vert.x

初创阶段，由于 Vert.x 功能全面、允许用多种语言开发，所以对初创小团队而言能够激发出较高的生产力。而且开发者社区活跃，所以技术虽新，却也不至于掉到坑里没人捞。

等到用户规模上去以后，Vert.x 具有 JVM 娘胎里带来的稳定性优势，而又通过类似 Node.js 的异步范式保证了多用户性能和可扩展性。据此分析，采用 Vert.x 做的软件项目如果一不小心起飞了，不用经历“脚本 -> Java”这种互联网业常见的转平台之痛。

总结一下，我们认为：

> **Vert.x 是一个不管“从0到1”还是“从1到N”阶段都适用的工具集。**

## Vert.x 参考项目及上手资料（不断更新中）

官方的 [vertx-examples](https://link.zhihu.com/?target=https%3A//github.com/vert-x3/vertx-examples) 应该是上手的最佳资料了。它与 Vert.x 同步更新，所有例子都给出了直接可以运行的工程文件，用 Intellij IDEA 等 IDE 可以直接跑。不过， vertx-examples 里面给出的都是一个个小品级的例程，拿来作为项目的参考还是零散了一点。

此外，由于Vert.x 不是框架，所以对代码的组织结构没有什么限制。这对于项目经验丰富的开发者而言是加分项，但对于初学者来说太 free style 了也会增加上手难度。

还有一点比较遗憾的是，Vert.x 还没有一套比较完整的 RealWorld demo. 基本上现在能找到的流行框架都把这个 Medium 的克隆项目写了一遍（ [Github 链接](https://link.zhihu.com/?target=https%3A//github.com/gothinkster/realworld)），方便大家上手和比较。但至今为止 Vert.x 的克隆还基本等于没有，有兴趣的同学可以追踪一下这个 [repo](https://link.zhihu.com/?target=https%3A//github.com/skanjo/realworld-vertx)。

下面把网上能够找到的 Vert.x 参考项目和教程汇总一下，方便初学者们找到一款与自己需求类似的原型，目的是通过阅读和重构来上手。评述部分比较侧重我们的可穿戴 IoT 需求，即数据实时性要求较高，几乎类似于网络游戏。

1）官方的用 Vert.x 搭建 Wiki 教程:

- 网址：[教程](https://link.zhihu.com/?target=http%3A//vertx.io/docs/guide-for-java-devs/)，[GitHub repo](https://link.zhihu.com/?target=https%3A//github.com/vert-x3/vertx-guide-for-java-devs)
- 要点：前后端通信，Verticle 间通信，异步模型
- 评述：如果做对实时性要求没有那么高的应用（非游戏、非 IoT），那么这个教程就非常够用了。

2）A multi-user realtime game by Burr Sutter:

- 网址：[Youtube 视频](https://link.zhihu.com/?target=https%3A//www.youtube.com/watch%3Fv%3DMydhJVPEnzU)，[GitHub repo](https://link.zhihu.com/?target=https%3A//github.com/burrsutter/vertx-game-server)
- 要点：Reactive 编程，eventbus 通信
- 评述：非常惊艳的现场演示，服务器—客户端实时互动，玩家间实时互动，绝对的一 demo 胜千言。不过除了几个视频以外没找到任何的文字教程，Github 上的文档也写的比较简单，所以没有实际测试过。但 Burr Sutter 的几次演讲视频的确是我们最初对 Vert.x 动心的原因。

3）Vert.x console:

- 网址：[GitHub repo](https://link.zhihu.com/?target=https%3A//github.com/yunyu/vertx-console)，[Youtube 演示视频](https://link.zhihu.com/?target=https%3A//www.youtube.com/watch%3Fv%3DI3ZWuOngesU)
- 要点：Vue.js，docker，实时监控
- 评述：由英语很棒的青年开发者 Yunyu Li 在 GSoC 2017 中贡献，由于 Vert.x 3.5.1 刚刚修复了此前 web client 的一个 bug，新版 vertx-console 还没来得及测试。整体上是一个完成度相当高的全栈项目，尤其是监控部分对于 IoT 应用非常关键。

4）InMood4Life:

- 网址：[博客文章](https://link.zhihu.com/?target=https%3A//medium.com/inmoodforlife/vert-x-the-perfect-reactor-for-iot-devices-5d0b3cb36a98)，[GitHub Repo](https://link.zhihu.com/?target=https%3A//github.com/BreizhBeans/InTheMood4Life)
- 要点： Gradle 项目，完全 Groovy 开发，IoT，外设协同
- 评述：众多上手项目中唯一一个不用 Java 开发的，使用了 Gradle + Groovy 这个组合。同时这个项目桥接了蓝牙设备，体现了 Vert.x 在软硬件协同项目中的前景。这类项目常见的上手难度在于没有那几样外设则无法跑起来，只能看看代码过过干瘾。

5）Vert.x starter:

- 网址：[https://vertx-starter.jetdrone.xyz/](https://link.zhihu.com/?target=https%3A//vertx-starter.jetdrone.xyz/), [视频演示](https://link.zhihu.com/?target=https%3A//www.youtube.com/watch%3Fv%3DWA9P-khaWI4)
- 要点：增减组件、配置依赖关系、前后端匹配
- 评述：Vert.x 主程之一 pmlopes 做的最小项目生成器，点点按钮就能生成工程文件。我们主要研究了一下用 Maven (Java/Kotlin)+ Vue.js 配置出来的起步工程，觉得是很好的全栈上手参考。Java + Maven 组合应该是作者主要精力所在，经测可用; Java + Gradle 的可配置项少一点，但也经测可用。Kotlin + Maven 生成项目有 bug，反映给作者后打了补丁，但也可以看出可能暂时不是维护重点。添加 Vert.x 的模块很方便，但只是添加了 maven 包依赖，没有起始代码。相比之下， Clojure 的 [Luminus](https://link.zhihu.com/?target=http%3A//www.luminusweb.net/) 项目功能与之类似，但每加一个包都会生成相应的起始代码，大大降低了上手难度。另外一个问题是几乎没有文档，牵涉到前端的运行过程要看上面的视频演示。Vert.x 官方也有一个 [starter](https://link.zhihu.com/?target=http%3A//start.vertx.io/)，与 pmlopes 自己的私房 xyz starter 功能类似，但是要简单一点。猜测两个 starter 同源，应该会合并。

5）一个基于 microservice 的实时商品报价平台样例

- 网址：[Vert.x - From zero to (micro)-hero](https://link.zhihu.com/?target=http%3A//escoffier.me/vertx-hol/)
- 要点：分布式、微服务 microservice、RxJava、实时监控
- 评述：重点推荐。Vert.x 主创人员 Clement Escoffier 和 Julien Viet 创作的一个内容翔实的教程，把框架代码写好了，让初学者把核心业务代码填到需要的地方去。提供了懒人式打开即用的源代码，最终的效果是一个有前后端、实时曲线、服务追踪的比较全面的样例工程。

6）Vertx Blueprint (三个项目）：

- 网址：[博客文章](https://link.zhihu.com/?target=http%3A//vertx.io/blog/vert-x-blueprint-tutorials/)，GitHub repo ([Todo](https://link.zhihu.com/?target=https%3A//github.com/sczyh30/vertx-blueprint-todo-backend), [Kue](https://link.zhihu.com/?target=https%3A//github.com/sczyh30/vertx-blueprint-job-queue), [Micro Shop](https://link.zhihu.com/?target=https%3A//github.com/sczyh30/vertx-blueprint-microservice))
- 要点： 前后端全栈、Eventbus 通信、微服务 microservice 架构、docker
- 评述： 由杭州网友 [sczyh30](https://link.zhihu.com/?target=https%3A//github.com/sczyh30/vertx-blueprint-microservice) 在 GSoC 2016 中贡献，三个例子循序渐进，质量很高。第三个 Micro Shop 的例子是一个全栈的电子商务网站，全部 docker 部署，啃下来就基本上明白 Vert.x microservice 在生产中怎么做了。不过 Micro Shop 跑起来需要自己配置 docker-machine 环境。在测试中发现代码与最新版本的 Keycloak 用户权限管理模块有点冲突，作者正在修改中。

7）VertxUp / VertxZero:

- 网址：[Vertx Zero Up Framework](https://link.zhihu.com/?target=http%3A//www.vertxup.cn/)
- 要点：Dependency Injection、与 Spring 的对应理解、复杂项目组织方式
- 评述：我们之前没有 Spring 开发经验，这个项目理解起来有些困难。但对于熟悉 Spring 的开发者是很好的迁移途径，由重庆网友 [silentbalanceyh](https://link.zhihu.com/?target=https%3A//github.com/silentbalanceyh) 开发。

8）基于Vert.x和RxJava 2构建通用的爬虫框架

- 网址：[文章](https://link.zhihu.com/?target=http%3A//blog.csdn.net/slfq6of5o7ah/article/details/79227286)，[GitHub repo](https://link.zhihu.com/?target=https%3A//github.com/fengzhizi715/NetDiscovery)
- 要点：爬虫、http client
- 评述：爬虫功能与我们这次的 IoT 应用稍有不同，但也是web app 中的一个常见需求。由网友沈哲开发。

9）另一个基于微服务的例子

- 网址：[文章](https://link.zhihu.com/?target=https%3A//piotrminkowski.wordpress.com/2017/08/24/asynchronous-microservices-with-vert-x/)，[GitHub repo](https://link.zhihu.com/?target=https%3A//github.com/piomin/sample-vertx-microservices.git)
- 要点：异步与 MongoDB 通信，vertx-config，Consul 服务发现
- 评述：主要阅读了教程文章，测试环境介绍得不太全所以没有实际测试。但项目就两个模块，结构比较清晰，适合初学时阅读理解。

10）[Clement Escoffier](https://link.zhihu.com/?target=https%3A//developers.redhat.com/blog/author/cescoffier/) 写的又一套新手教程

- 网址：[文章](https://link.zhihu.com/?target=https%3A//developers.redhat.com/blog/2018/03/13/eclipse-vertx-first-application/)，[GitHub repo](https://link.zhihu.com/?target=https%3A//github.com/redhat-developer/introduction-to-eclipse-vertx)
- 要点： web app 入门，异步通讯，简单 REST，RxJava 解决 callback hell
- 评述：主要开发者 Clement 可谓在 Vert.x 的教学推广上不遗余力。可惜的是仍然没有一个完整的类似 RealWorld demo (conduit) 的例子。不过 vertx-examples 里面的例子是很全的，对于不想当伸手党的新手来说，其实弹药充足的很。

11）采用 Vert.x 和 RxJava 做数据缓存

- 网址：[文章](https://link.zhihu.com/?target=https%3A//blog.danlew.net/2015/06/22/loading-data-from-multiple-sources-with-rxjava/)，[Github repo](https://link.zhihu.com/?target=https%3A//github.com/dlew/rxjava-multiple-sources-sample)
- 要点： vert.x 做缓存，适用场合类似 Redis 但据说性能更高，原因是节省了很多 IO
- 评述：新手如果学做 Hello world 应用应该不需要这篇教程。然而缓存是任何严肃应用很早期就会遇到的问题。个人认为如果在 vert.x 中再开设一个 redis，好处是搭积木起应用快，坏处是需要多维护一个 redis 服务。这对初创团队来说增加了部署复杂度，也分散运维精力。考虑到 vert.x+postgres 几乎是可以标配到所有初创应用，只依赖这2个标配工具如果能顺带做起高性能缓存，那么对初创团队而言是节省精力的。

12）在 Vert.x 上面做 REST 的一个教程

- 网址：[文章](https://link.zhihu.com/?target=https%3A//medium.com/pharos-production/vert-x-restful-services-on-java-6a4ed8a30489)，[GitHub repo](https://link.zhihu.com/?target=https%3A//github.com/PharosProduction/tutorial-vertx-rest-on-java)
- 要点：平实的一篇 Vert.x + REST 的教程，不多不少
- 评述：难得有一篇从 Maven 配置开始讲起，小白友好，尤其是对非 Java 生态转过来的开发者友好。有项目以后自己从头开始建 Maven 工程的情况并不多，但是这种手把手的教程对于新手是非常有意义的。

13）在 Vert.x 与图数据库 Neo4j 联用

- 网址：[文章](https://link.zhihu.com/?target=https%3A//levelup.gitconnected.com/reactive-apis-for-graph-data-vert-x-neo4j-335521321fa3)，[GitHub repo](https://link.zhihu.com/?target=https%3A//github.com/pvub/demos/tree/master/graph/GraphDemo)
- 要点：航班路线可以用图数据库建模，这种可视化软件是有真实需求的，尤其是2022年3月的不幸事件后。
- 评述：很多数据项目都是从玩具级别的摆弄数据开始，如果真的要严肃做成应用，一般第一个问题就是如何包装成web service. Vert.x和图数据库的联动就能解决这个小团队起步问题。

14）以 Clojure 语言开发 Vert.x

- 网址：[文章](https://zhuanlan.zhihu.com/p/32895240)，[GitHub repo](https://link.zhihu.com/?target=https%3A//github.com/vertx-china/vertx-lang-clojure)

- 要点：由

   

  Vertx 中国站

   

  发起的 vertx-lang-clojure 项目，采用 codegen 将 Java 语法的 vert.x 源代码直译成 Clojure 代码，可以看做是社区版的 Vert.x Clojure 多语种支持. 其 GitHub 库的发起者是

   

  [@圆胖肿](https://www.zhihu.com/people/019f06627adc51d9cd030517a6f14c26)

   

  ，我们也贡献了一部分代码，目前适配到 vert.x 4.1.0版本。

- 评述：小团队用Clojure非常合适。老鸟可以用 Clojure 快速编写领域逻辑，再挂到 verticle 上。Vert.x虽然提倡多语种，但是官方并没有 Clojure 绑定，所以很难用这种高效简洁的 LISP 来编写 Vert.x verticle. 我们发现，1名 Clojure 领域逻辑 +1名 JS 前端 + 1名 Java 应用网关，这种2-3人的小团队做实时多人在线应用的速度很快。而且做出来的不是小品级的概念验证，是真正可以上生产撑起来高频多发访问的产品。