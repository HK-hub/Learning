# 短链接生产器



## 调研



**查询关键字：短链接**

![image-20221115183011311](https://raw.githubusercontent.com/HK-hub/picQuick/images/typora/20221115183011.png)

| 标题                                                         | 类型 | 链接                                                         | 描述                             | 优点                                               | 缺点                                  |
| ------------------------------------------------------------ | ---- | ------------------------------------------------------------ | -------------------------------- | -------------------------------------------------- | ------------------------------------- |
| [短链接系统的设计](https://my.oschina.net/langwanghuangshifu/blog/4286543) | 文章 | [原文链接](https://my.oschina.net/langwanghuangshifu/blog/4286543) | 自增序列，hash映射算法           | 讲解了百度和微博短链接的基本实现思路               | 文章描述较为简略，只停留于理论        |
| [**ohUrlShortener** 短链接服务系统](https://www.oschina.net/p/ohurlshortener) | 软件 | [软件链接](https://www.oschina.net/news/190788)              | Go 语言下的短链接生成器          | 提供了基本的短链接系统功能：生成，访问，数据统计   | 提供的数据统计功能不多；Go 语言系列   |
| [最近学到的「短链接」知识](https://my.oschina.net/u/3777556/blog/5130549) | 文章 | [原文链接](https://my.oschina.net/u/3777556/blog/5130549)    | 自增序列                         | 简单介绍了自增id实现方法                           | 文章描述较为简略，只停留于理论        |
| [**Shortme** URL 短链接服务](https://www.oschina.net/p/shortme) | 软件 | [软件链接](https://www.oschina.net/p/shortme)                | Go 语言下的短链接生成器          | 使用Hash,Simple, Base 三种方法简单的实现短链接生成 | 只提供了短链接的生成功能; Go 语言系列 |
| [链接太长，自己设计个短链接服务吧](https://my.oschina.net/yinjihuan/blog/4669601) | 文章 | [原文链接](https://my.oschina.net/yinjihuan/blog/4669601)    | 简单demo，谈到了并发和分布式问题 | 简单demo，谈到了并发和分布式问题                   | 简单的基于62进制；简单demo 示例代码   |
| [如何生成高性能的短链接？](https://my.oschina.net/u/3471412/blog/4426134) | 文章 | [原文链接](https://my.oschina.net/u/3471412/blog/4426134)    | 讲述了MurmurHash，自增序列       | 优化了Hash 算法，介绍了短链接生产器的性能优化      | 生成算法较为局限只停留在理论分析      |
| [短链服务方案](https://my.oschina.net/u/2485991/blog/546946) | 文章 | [原文链接](https://my.oschina.net/u/2485991/blog/546946)     | 依靠数据库ID反向生成             | 逆向思维，将铭感的数据库ID进行加密作为短链接       | 生成方法存在局限性                    |
|                                                              |      |                                                              |                                  |                                                    |                                       |
|                                                              |      |                                                              |                                  |                                                    |                                       |

[详细介绍了其中分布式ID算法](https://my.oschina.net/u/3471412/blog/5180993)

[另辟蹊径 -- 自增短链 ID 设计](https://my.oschina.net/u/2485991/blog/613920)

[带密码，访问次数，访问时间](https://my.oschina.net/tinyframework/blog/866605)



