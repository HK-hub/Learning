# 聊聊Spring IoC

[toc]

# Spring IoC容器实现分类

Spring提供了两种容器类型：**BeanFactory和ApplicationContext**。

-  BeanFactory。基础类型IoC容器，提供完整的IoC服务支持。如果没有特殊指定，**默认采用延 迟初始化策略（lazy-load**）。只有当客户端对象需要访问容器中的某个受管对象的时候，才对 该受管对象进行初始化以及依赖注入操作。所以，相对来说，容器启动初期速度较快，所需 要的资源有限。<u>对于资源有限，并且功能要求不是很严格的场景，BeanFactory是比较合适的</u><u>IoC容器选择。</u>

- ApplicationContext。**ApplicationContext在BeanFactory的基础上构建，是相对比较高 级的容器实现**，除了拥有BeanFactory的所有支持，ApplicationContext还提供了其他高级特性，比如事件发布、国际化信息支持等，这些会在后面详述。ApplicationContext所管理 的对象，**在该类型容器启动之后，默认全部初始化并绑定完成**。所以，相对于BeanFactory来 说，ApplicationContext要求更多的系统资源，同时，因为在启动时就完成所有初始化，容 器启动时间较之BeanFactory也会长一些。<u>在那些系统资源充足，并且要求更多功能的场景中，ApplicationContext类型的容器是比较合适的选择</u>。

![image-20220308215535468](https://s2.loli.net/2022/03/08/2bNXmTHiFusBSU8.png)



# BeanFactory

BeanFactory，顾名思义，就是<u>生产Bean的工厂</u>。**BeanFactory就像一个汽车生产厂**。你从其他汽车零件厂商或者自己的零件生产部门取得汽车零件送入这个汽车生产厂，最后，只需要从生产线的终点取得成品汽车就可以了。相似地，将应用所需 的所有业务对象交给BeanFactory之后，剩下要做的，就是直接从BeanFactory取得最终组装完成并且可用的对象。至于这个最终业务对象如何组装，你不需要关心，BeanFactory会帮你搞定。

**BeanFactory 的定义如下：**

```java
public interface BeanFactory { 
    String FACTORY_BEAN_PREFIX = "&";
	Object getBean(String name) throws BeansException;    
	Object getBean(String name, Class requiredType) throws BeansException; /**
    * @since 2.5 */
    Object getBean(String name, Object[] args) throws BeansException; boolean containsBean(String name);
    boolean isSingleton(String name) throws NoSuchBeanDefinitionException; /**
    * @since 2.0.3 */
    boolean isPrototype(String name) throws NoSuchBeanDefinitionException; /**
    * @since 2.0.1 */
    boolean isTypeMatch(String name, Class targetType) throws NoSuchBeanDefinitionException; Class getType(String name) throws NoSuchBeanDefinitionException;
    String[] getAliases(String name);
}
```



## BeanFactory的对象注册与依赖绑定方式

BeanFactory作为一个IoC Service Provider，为了能够明确管理各个业务对象以及业务对象之间的 依赖绑定关系，同样需要某种途径来记录和管理这些信息。在介绍IoC Service Provider时，我们提到通常会有三种方式来管理这些信息。而BeanFactory几乎支持所有这些方式，很令人兴奋，不是吗？

<u>BeanFactory只是一个接口，我们最终需要一个该接口的实现来进行实际的Bean的管理</u>，DefaultListableBeanFactory就是这么一个比较通用的BeanFactory实现类。DefaultListableBeanFactory除了间接地实现了BeanFactory接口，还实现了B**eanDefinitionRegistry接口，该接口才 是在BeanFactory的实现中担当Bean注册管理的角色**。基本上，BeanFactory接口只定义如何访问容 器内管理的Bean的方法，各个BeanFactory的具体实现类负责具体Bean的注册以及管理工作。 **BeanDefinitionRegistry接口定义抽象了Bean的注册逻辑**。通常情况下，具体的BeanFactory实现类会实现这个接口来管理Bean的注册。它们之间的关系如图4-3所示：

![image-20220308221015215](https://s2.loli.net/2022/03/08/swBgQK1iu2mWlP3.png)



打个比方说，<u>BeanDefinitionRegistry就像图书馆的书架</u>，所有的书是放在书架上的。虽然你 还书或者借书都是跟**图书馆（也就是BeanFactory，或许BookFactory可能更好些**）打交道，但书架才 是图书馆存放各类图书的地方。所以，书架相对于图书馆来说，就是它的“BookDefinitionRegistry”。
		每一个受管的对象，在容器中都会有一个**BeanDefinition**的实例（instance）与之相对应，该 BeanDefinition的实例负责保存对象的所有必要信息，包括其对应的对象的class类型、是否是抽象 类、构造方法参数以及其他属性等。当客户端向BeanFactory请求相应对象的时候，BeanFactory会 通过这些信息为客户端返回一个完备可用的对象实例。RootBeanDefinition和ChildBeanDefinition是BeanDefinition的两个主要实现类。

