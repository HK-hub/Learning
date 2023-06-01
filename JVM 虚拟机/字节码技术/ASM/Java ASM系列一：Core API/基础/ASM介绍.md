# ASM 介绍

[toc]

## 1. ASM是什么？

简单来说，**[ASM](https://asm.ow2.io/)是一个操作Java字节码的类库**。

为了能够更好的理解ASM是什么，我们需要来搞清楚两个问题：

- 第一个问题，**ASM的操作对象是什么呢？**
- 第二个问题，**ASM是如何处理字节码（ByteCode）数据的呢？**

首先，我们来看第一个问题：**ASM的操作对象是什么呢？** 回答：ASM所操作的对象是字节码（ByteCode）数据。

我们都知道，一个`.java`文件经过Java编译器（`javac`）编译之后会生成一个`.class`文件。 在`.class`文件中，存储的是字节码（ByteCode）数据，如下图所示。ASM所操作的对象就是字节码（ByteCode），而在许多情况下，字节码（ByteCode）的具体表现形式是`.class`文件。

![From Java to Class](https://lsieun.github.io/assets/images/java/javac-from-dot-java-to-dot-class.jpeg)

------

接着，我们来看第二个问题：**ASM是如何处理字节码（ByteCode）数据的呢？** 回答：ASM处理字节码（ByteCode）的方式是“拆分－修改－合并”。

ASM处理字节码（ByteCode）数据的思路是这样的：第一步，将`.class`文件拆分成多个部分；第二步，对某一个部分的信息进行修改；第三步，将多个部分重新组织成一个新的`.class`文件。

在[Wikipedia](https://en.wikipedia.org/wiki/ObjectWeb_ASM)上，对ASM进行了如下描述：

ASM provides a simple API for decomposing(将一个整体拆分成多个部分), modifying(修改某一部分的信息), and recomposing(将多个部分重新组织成一个整体) binary Java classes (i.e. ByteCode).

## 2. ASM的过去和现在

### 2.1. ASM的过去

对于ASM的过去，主要说明三个问题：

- 第一个问题，ASM从什么时候开始出现的？
- 第二个问题，ASM的作者是谁？
- 第三个问题，ASM的名字有什么含义？

在**2002年**的时候，**Eric Bruneton**、Romain Lenglet和Thierry Coupaye发表了一篇文章，名为《[ASM: a code manipulation tool to implement adaptable systems](https://lsieun.github.io/assets/pdf/asm-eng.pdf)》。在这篇文章当中，他们提出了ASM的设计思路。

一般来说，大写字母的组合，可能是多个单词的缩写形式，例如，JVM表示“Java Virtual Machine”。但是，ASM并不是多个单词的首字母缩写形式。在上面的文章中，记录了下面的话：

> The ASM name does not mean anything: it is just a reference to the `__asm__` keyword in C, which allows some functions to be implemented in assembly language.

### 2.2. ASM的现在

对于ASM的现在，主要说明两个问题：

- 第一个问题，ASM属于哪一个机构？
- 第二个问题，ASM的Logo是什么样的？

The ASM library is a project of the [OW2 Consortium](https://www.ow2.org/). OW2 is an independent, global, **open-source software community**.

作为一个小故事，我们来说一下OW2组织是如何形成的。OW2组织的形成，与中国的一些大学和公司也有很大的关系（原文内容来自[这里](https://www.ow2.org/view/About/OW2_Introduction)）：

- 2002年，ObjectWeb项目启动，它是由INRIA、Bull和France Telecom共同开发的项目，并形成了一个成熟的、开源软件社区。
- 2004年，Orientware项目启动，由中国的北京大学、北航、国防科技大学、中创软件和中国科学院软件研究所共同研发。
- 2005年，ObjectWeb和Orientware签署了一份协议，决定共享代码库，一起开发中间件软件。
- 2006年，ObjectWeb和Orientware两个社区组织融合，形成了OW2组织。OW2组织名字，可能是由**O**bject**W**eb和**O**rient**w**are名称当中的两组`O`和`W`组合而来。

ASM的Logo设计很有特点，它在旋转的过程中，会分别呈现出“A”、“S”和“M”这三个字母，如下图所示：

![ASM Logo](https://lsieun.github.io/assets/images/java/asm/asm-logo.gif)

### 2.3. ASM的版本发展

对于ASM版本的发展，我们要说明两点：

- 第一点，Java语言在不断发展，那么，ASM版本也要不断发展来跟得上Java的发展。
- 第二点，在选择ASM版本的时候，要注意它支持的Java版本，来确保兼容性。

比如说，我们常用的Java版本是Java 8和Java 11。针对Java 8版本，我们需要使用ASM 5.0版本，就能正常工作。对于Java 11版本，我们需要使用ASM 7.0版本，就能正常工作。当然，我们可以尽量使用较高的ASM版本。

| ASM Release | Release Date | Java Support                              |
| ----------- | ------------ | ----------------------------------------- |
| 2.0         | 2005-05-17   | Java 5 language support                   |
| 3.2         | 2009-06-11   | support for the new `invokedynamic` code. |
| 4.0         | 2011-10-29   | Java 7 language support                   |
| 5.0         | 2014-03-16   | **Java 8 language support**               |
| 6.0         | 2017-09-23   | Java 9 language support                   |
| 6.1         | 2018-03-11   | Java 10 language support                  |
| 7.0         | 2018-10-27   | **Java 11 language support**              |
| 7.1         | 2019-03-03   | Java 13 language support                  |
| 8.0         | 2020-03-28   | Java 14 language support                  |
| 9.0         | 2020-09-22   | Java 16 language support                  |
| 9.1         | 2021-02-06   | **JDK 17 support**                        |
| 9.2         | 2021-06-26   | JDK 18 support                            |
| 9.3         | 2022-04-04   |                                           |
| 9.4         | 2022-10-02   |                                           |

## 3. ASM能够做什么

### 3.1. 通俗的理解

- 父类：修改成一个新的父类
- 接口：添加一个新的接口、删除已有的接口
- 字段：添加一个新的字段、删除已有的字段
- 方法：添加一个新的方法、删除已有的方法、修改已有的方法
- ……（省略）

```
public class HelloWorld extends Object implements Cloneable {
    public int intValue;
    public String strValue;

    public int add(int a, int b) {
        return a + b;
    }

    public int sub(int a, int b) {
        return a - b;
    }

    @Override
    public Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}
```

### 3.2. 专业的描述

ASM is an all-purpose(多用途的；通用的) Java ByteCode **manipulation**(这里的manipulation应该是指generate和transform操作) and **analysis** framework. It can be used to modify existing classes or to dynamically generate classes, directly in binary form.

The goal of the ASM library is to **generate**, **transform** and **analyze** compiled Java classes, represented as byte arrays (as they are stored on disk and loaded in the Java Virtual Machine).

![What ASM Can Do](https://lsieun.github.io/assets/images/java/asm/what-asm-can-do.png)

- **Program analysis**, which can range from a simple syntactic parsing to a full semantic analysis, can be used to find potential bugs in applications, to detect unused code, to reverse engineer code, etc.
- **Program generation** is used in compilers. This includes traditional compilers, but also stub or skeleton compilers used for distributed programming, Just in Time compilers, etc.
- **Program transformation** can be used to optimize or obfuscate programs, to insert debugging or performance monitoring code into applications, for aspect oriented programming, etc.

小总结：

- **generation：是从0到1的操作，是最简单的操作，在第二章进行介绍。**也就是说，原来什么都没有，经过generation操作，会生成一个新的`.class`文件。
- **transformation：是从1到1的操作，是中度复杂的操作，在第三章进行介绍。**也就是说，原来有一个`.class`文件，经过transformation操作，会生成一个新的`.class`文件。
- **analysis：是从1到0的操作，是最复杂的操作。**也就是说，原来有一个`.class`文件，经过analysis操作，虽然有分析的结果，但是不会生成新的`.class`文件。

在这里，希望**大家记忆这三个单词：generation、transformation和analysis。**因为这三个单词，用**精简、准确**的语言来描述ASM能够做什么。

## 4. 为什么要学习ASM？

平常，我们使用Java语言进行开发，能够解决很多的问题。我们可以把Java语言解决问题的范围称之为“Java语言的世界”。那么，ASM起什么作用呢？**ASM就是一处位于“Java语言的世界”边界上的一扇大门，通过这扇大门，我们可以前往“字节码的世界”。**在“字节码的世界”里，我们会看到不一样的“风景”，能够解决不一样的“问题”。

![img](https://lsieun.github.io/assets/images/java/asm/java-language-asm-byte-code.png)

ASM往往在一些框架的底层起着重要的作用。接下来，我们介绍**两个关于ASM的应用场景：Spring和JDK**。这两个应用场景例子的目的，就是希望大家了解到ASM的重要性。

### 4.1. Spring当中的ASM

**第一个应用场景，是Spring框架当中的AOP。** 在很多Java项目中，都会使用到Spring框架，而Spring框架当中的AOP（Aspect Oriented Programming）是依赖于ASM的。具体来说，Spring的AOP，可以通过JDK的动态代理来实现，也可以通过CGLIB实现。其中，**CGLib** (**C**ode **G**eneration **Lib**rary)是在**ASM**的基础上构建起来的，所以，Spring AOP是间接的使用了ASM。（参考自[Spring Framework Reference Documentation](https://docs.spring.io/spring-framework/docs/3.0.0.M3/reference/html/index.html)的[8.6 Proxying mechanisms](https://docs.spring.io/spring-framework/docs/3.0.0.M3/reference/html/ch08s06.html)）。

### 4.2. JDK当中的ASM

**第二个应用场景，是JDK当中的Lambda表达式**。 在Java 8中引入了一个非常重要的特性，就是支持Lambda表达式。Lambda表达式，允许把方法作为参数进行传递，它能够使代码变的更加简洁紧凑。但是，我们可能没有注意到，其实，**在现阶段（Java 8版本），Lambda表达式的调用是通过ASM来实现的**。

在`rt.jar`文件的`jdk.internal.org.objectweb.asm`包当中，就包含了JDK内置的ASM代码。在JDK 8版本当中，它所使用的ASM 5.0版本。

如果我们跟踪Lambda表达式的编码实现，就会找到`InnerClassLambdaMetafactory.spinInnerClass()`方法。在这个方法当中，我们就会看到：JDK会使用`jdk.internal.org.objectweb.asm.ClassWriter`来生成一个类，将lambda表达式的代码包装起来。

- LambdaMetafactory.metafactory() 第一步，找到这个方法
    - InnerClassLambdaMetafactory.buildCallSite() 第二步，找到这个方法
        - InnerClassLambdaMetafactory.spinInnerClass() 第三步，找到这个方法

注意：在《[Java ASM系列二：OPCODE](https://lsieun.github.io/java/asm/java-asm-season-02.html)》的第三章中的[Java 8 Lambda](https://lsieun.github.io/java-asm-02/java8-lambda.html)对Lambda实现进行了较为详细的介绍。

## 5. 总结

本文主要是对ASM进行简单的介绍，希望大家能够对ASM有基本的了解，并没有涉及到“技术性”的知识点，因此也不需要“记忆”任何内容，了解一下就够了。

本文内容总结如下：

- 第一点，ASM所处理对象是**字节码数据**，也可以直观的理解成`.class`文件，因此ASM并不是针对`.java`文件进行处理。
- 第二点，ASM能够对**字节码数据**进行哪些操作呢？回答：analyze、generate、transform。
- 第三点，ASM可以形象的理解为“Java语言世界”边缘上的一扇大门，通过这扇大门，可以帮助我们进入到“**字节码**的世界”。