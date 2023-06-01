[toc]

---

# 前言

随着我们 GraphQL 的产生，发展，完善，经过了我们GraphQL 官方API文档的规范约束，到我们各自的第三方语言实现方式，再到最后的我们 Spring 行业龙头和GraphQL Java 开发团队的组队一起开发出了我们未来 Java 行业领域内将会长期维护和支持的 GraphQL 标准以及实现。

对于此处呢，虽然目前主流市场上对于GraphQL 的使用和推广力度都不大，甚至是说在我们的国内开发中，基本没有任何互联网项目，厂商使用此项，技术。这其中呢， 多种多样的原因，国内厂商都比较推荐的是使用稳定的，有良好生态的支持的技术，再者呢就是因为如果需要使用这们技术，那么就需要我们的前后端人员都分别知晓GraphQL 的一个约束和书写.... 

但是小编本人呢只是想说：坚持稳定技术没有错，但是我们不能因为老的技术稳定，应用广泛而去排斥我们的新的技术，这样对于我们的新的技术是不公平的。下面将 [Spring 官方的关于GraphQL 的发布博客](https://spring.io/blog/2021/07/06/hello-spring-graphql)分享出来，后续呢小编将继续学习 Spring GraphQL 随后将入门教程书写出来，如果对GraphQL 不大了解的可以去看我之前写的 [GraphQL 简单入门]() 。 

## [你好，Spring GraphQL](https://spring.io/blog/2021/07/06/hello-spring-graphql)

**[Engineering](https://spring.io/blog/category/engineering) | [Rossen Stoyanchev](https://spring.io/team/rstoyanchev)** **| July 06, 2021 | [10 Comment](https://spring.io/blog/2021/07/06/hello-spring-graphql#disqus_thread)**

>
>
>**客座作者**：[Andi Marek](https://twitter.com/andimarek)，GraphQL Java 创始人
>
>

我很高兴地宣布[Spring GraphQL](https://github.com/spring-projects/spring-graphql)项目的创建以及面向 1.0 版本的初始里程碑的可用性。该项目集成了[GraphQL Java](https://www.graphql-java.com/)和 Spring，[并由](https://www.graphql-java.com/)两个团队合作开发。

今天是 GraphQL Java 的 6 岁生日！**我从一开始就做出的一个基本决定是将任何 HTTP 和 IO 方面作为单独的关注点**。GraphQL Java 一直“只是”一个执行 GraphQL 请求的引擎。这个决定已经得到了回报，但明显的缺点是需要为现实世界的使用创建自己的 HTTP 适配器。

这导致多年来为 Spring 创建了大量 GraphQL 集成，包括来自 GraphQL Java 团队的[GraphQL Java Spring](https://github.com/graphql-java/graphql-java-spring)项目。

但坦率地说，我一直渴望一流的 Spring 集成。

大约一年前到现在，GraphQL Java 和 Spring 团队举行了第一次会议，讨论如何实现这一目标。

在过去的 12 个月中，尽管时区差异具有挑战性，但我们在广泛的主题上进行了合作和讨论。我们现在准备通过里程碑版本将这项工作带给更广泛的受众。

特别感谢[Rossen](https://spring.io/team/rstoyanchev)和[Brian](https://spring.io/team/bclozel)的大力合作，以及[Rob](https://spring.io/team/rwinch)和[Mark](https://spring.io/team/mp911de)以及越来越多的 Spring 工程师加入这项工作。

我真的相信这个项目对于 GraphQL Java 和更广泛的 GraphQL 生态系统来说是巨大的一步：由 Spring 工程师维护和发展的 Spring 集成是 GraphQL 成功的关键因素。

[Spring GraphQL](https://github.com/spring-projects/spring-graphql)是[GraphQL Java Spring](https://github.com/graphql-java/graphql-java-spring)的继承者。目的是让 Spring GraphQL 成为所有 Spring GraphQL 应用程序的基础，进而构建在 GraphQL Java 上。

我们对 GraphQL Java 和 Spring GraphQL 的总体理念是不偏不倚，专注于全面和广泛的支持。我们希望 Spring 和 GraphQL Java 的类似现有集成将选择构建在 Spring GraphQL 上，而不是专注于提供更高级别的支持和更多自以为是的功能。

一个非常重要的目标是听取社区您的意见，帮助我们稳定 API 和面向 1.0 版本的功能集。如果您有 GraphQL 应用程序，请查看并通过[打开问题](https://github.com/spring-projects/spring-graphql/issues)让我们知道。

请阅读[后续博客](https://spring.io/blog/2021/07/06/introducing-spring-graphql)文章以了解更多详细信息。

GraphQL Java 和 Spring 团队将于9 月 2 日至 3 日在[SpringOne](https://springone.io/)上进行联合演讲。请注册参加会议，该会议在线且连续第二年免费。

[安迪·马雷克](https://twitter.com/andimarek)



[由Disqus提供支持的评论](https://disqus.com/)