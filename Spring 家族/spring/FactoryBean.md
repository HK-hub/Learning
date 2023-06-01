# FactoryBean 是什么?

[toc]

# 工厂方法与FactoryBean

在强调“面向接口编程”的同时，有一点需要注意：虽然对象可以通过声明接口来避免对特定接 口实现类的过度耦合，但总归需要一种方式将声明依赖接口的对象与接口实现类关联起来。否则，只依赖一个不做任何事情的接口是没有任何用处的。

通常的做法是通过使用工厂方法（Factory Method）模式，提供一个工厂类来实例化具体的接口 实现类，这样，主体对象只需要依赖工厂类，具体使用的实现类有变更的话，只是变更工厂类，而主体对象不需要做任何变动。

![image-20220310083254443](https://s2.loli.net/2022/03/10/yDVF9kvocMKn2iJ.png)

针对使用工厂方法模式实例化对象的方式，Spring的IoC容器同样提供了对应的集成支持。我们所 要做的，只是将工厂类所返回的具体的接口实现类注入给主体对象



## 静态工厂方法（Static Factory Method）

为了将该静态工厂方法类返回的实现注入Foo，我们使用以下方式进行配置（通过setter方法注入 方式为Foo注入BarInterface的实例）：

````xml
<bean id="foo" class="...Foo">
    <property name="barInterface"> 
        <ref bean="bar"/>
    </property>
</bean>
<bean id="bar" class="...StaticBarInterfaceFactory" factory-method="getInstance"/>
````

**class指定静态方法工厂类，factory-method指定工厂方法名称**，然后，容器调用该静态方法工 厂类的指定工厂方（getInstance），并返回方法调用后的结果，即BarInterfaceImpl的实例。 也就是说，为foo注入的bar实际上是BarInterfaceImpl的实例，即方法调用后的结果，而不是静态 工厂方法类（StaticBarInterfaceFactory）。我们可以实现自己的静态工厂方法类返回任意类型的对象实例，但工厂方法类的类型与工厂方法返回的类型没有必然的相同关系。



## 非静态工厂方法（Instance Factory Method）

既然可以将静态工厂方法实现类的工厂方法调用结果作为bean注册到容器中，我们同样可以针对 基于工厂类实例的工厂方法调用结果应用相同的功能:

现在为BarInterface提供非静态的工厂方法实现类，该类定义如下代码所示：

```java
 public class NonStaticBarInterfaceFactory
{
    public BarInterface getInstance() {
    	return new BarInterfaceImpl(); 
    } 
     ...
}
```

因为工厂方法为非静态的，我们只能通过某个NonStaticBarInterfaceFactory实例来调用该方 法（哦，错了，是容器来调用），那么也就有了如下的配置内容：

```xml
<bean id="foo" class="...Foo"> 
    <property name="barInterface"> 
        <ref bean="bar"/> 
    </property>
</bean> 
<bean id="barFactory" class="...NonStaticBarInterfaceFactory"/>

< bean id="bar" factory-bean="barFactory" factory-method="getInstance"/>
```

NonStaticBarInterfaceFactory是作为正常的bean注册到容器的，而bar的定义则与静态工厂方 法的定义有些不同。现在使factory-bean属性来指定工厂方法所在的工厂类实例，而不是通过 class属性来指定工厂方法所在类的类型。指定工厂方法名则相同，都是通过factory-method属性进行的。





# FactoryBean 

<u>FactoryBean是Spring容器提供的一种可以**扩展容器对象实例化逻辑的接口**</u>，请不要将其与容器 名称BeanFactory相混淆。FactoryBean，其主语是Bean，定语为Factory，也就是说，**它本身与其他注 册到容器的对象一样，只是一个Bean而已**，只不过，这种类型的Bean本身就是生产对象的工厂（Factory）。

当某些对象的**实例化过程过于烦琐，通过XML配置过于复杂**，使我们宁愿使用Java代码来完成这 个实例化过程的时候，或者，某些**第三方库不能直接注册到Spring容器的时候**，就可以实现org.springframework.beans.factory.**FactoryBean接口**，给出自己的对象实例化逻辑代码。

要实现并使用自己的FactoryBean其实很简单，org.springframework.beans.factory. FactoryBean只定义了三个方法，如以下代码所示：

```java
public interface FactoryBean {
    Object getObject() throws Exception; 
    Class getObjectType(); 
    boolean isSingleton();
}
```

Spring容器内部许多地方了使用FactoryBean。下面是一些比较常见的FactoryBean实现，你可 以参照FactoryBean的Javadoc以了解更多内容。

- `JndiObjectFacoryBean`
- `LocalSessionFectoryBean`
- `ProxyFactoryBean`
- `TransactionProxyFactoryBean`



