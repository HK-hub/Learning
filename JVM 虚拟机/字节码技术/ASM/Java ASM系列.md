# Java ASM系列

[toc]

ASM is an open-source java library for manipulating bytecode.Note that the scope of the ASM library is strictly limited to reading, writing, transforming and analyzing classes. In particular the class loading process is out of scope.

> ASM 是一个开源的用于操作字节码的Java 类库



## 1. 主要内容

### 1.1. 文章

文章列表：

- 《[Java ASM系列一：Core API](https://lsieun.github.io/java/asm/java-asm-season-01.html)》主要是针对ASM当中Core API的内容进行展开。
- 《[Java ASM系列二：OPCODE](https://lsieun.github.io/java/asm/java-asm-season-02.html)》主要是针对`MethodVisitor.visitXxxInsn()`方法与200个opcode之间的关系展开，同时也会涉及到opcode对于Stack Frame的影响。
- 《[Java ASM系列三：Tree API](https://lsieun.github.io/java/asm/java-asm-season-03.html)》主要是针对ASM当中Tree API的内容进行展开。
- 《[Java ASM系列四：代码模板](https://lsieun.github.io/java/asm/java-asm-season-04.html)》主要是整理ASM代码，将常用的功能编写成“模板”，在使用时进行必要的修改，才能使用。
- 《[Java ASM系列五：源码分析](https://lsieun.github.io/java/asm/java-asm-season-05.html)》主要是对ASM源代码进行介绍。

### 1.2. 视频

视频列表：

| Website  | Core API(90个视频)                                           | OPCODE(41个视频)                                             | Tree API(52个视频)                                           |
| -------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 51CTO    | [Core API](https://edu.51cto.com/course/28517.html)          | [OPCODE](https://edu.51cto.com/course/28870.html)            | [Tree API](https://edu.51cto.com/course/29459.html)          |
| Bilibili | [All In One = Core API + OPCODE + Tree API](https://space.bilibili.com/1321054247/channel/seriesdetail?sid=381716) |                                                              |                                                              |
| Youtube  | [Core API](https://www.youtube.com/playlist?list=PLMxK51MH9Tart9g54B7FsvAioqCGXLOS9) | [OPCODE](https://www.youtube.com/playlist?list=PLMxK51MH9TapRian5wb0Zqu0UJ9mUcF0q) | [Tree API](https://www.youtube.com/playlist?list=PLMxK51MH9Taoha4D5PDCNPnInxZIsSxNn) |

需要注意的一点，在B站当中，Java ASM的系列一、二、三的视频放到了一个视频列表里：

- 编号为001~090的视频属于系列一
- 编号为101~141的视频属于系列二
- 编号为201~252的视频属于系列三

### 1.3. 代码

代码仓库：

- [Gitee](https://gitee.com/lsieun/learn-java-asm)
- [Github](https://github.com/lsieun/learn-java-asm)

### 1.4. 直播

- 《[Java ASM字节码操作（已结束）](https://lsieun.github.io/java/asm/java-asm-live.html)》主要是对《Java ASM系列一：Core API》和《Java ASM系列三：Tree API》内容的精简，能够快速上手使用ASM，进行一些常用的操作。

### 1.5. 网盘下载

JavaASM系列（百度网盘）

- 链接：https://pan.baidu.com/s/1P9_ja4VXYXw99cadRV97eg
- 提取码：`fsnf`

JavaASM系列（阿里云盘）

- 链接：https://www.aliyundrive.com/s/HKGRaTSFV3N
- 提取码: `dt64`

## 2. 参考资料

ASM相关：

- [ASM官网](https://asm.ow2.io/)
- ASM源码：[GitLab](https://gitlab.ow2.org/asm/asm)
- ASM API文档：[javadoc](https://asm.ow2.io/javadoc/index.html)
- ASM使用手册：[英文版](https://asm.ow2.io/asm4-guide.pdf)、[中文版](https://www.yuque.com/mikaelzero/asm)
- [ASM mailing list](https://mail.ow2.org/wws/info/asm)
- 参考文献
    - 2002年，[ASM: a code manipulation tool to implement adaptable systems(PDF)](https://lsieun.github.io/assets/pdf/asm-eng.pdf)
    - 2007年，[Using the ASM framework to implement common Java bytecode transformation patterns(PDF)](https://lsieun.github.io/assets/pdf/asm-transformations.pdf)

Oracle相关文档：

- Oracle: The Java Virtual Machine Specification, Java SE 8 Edition
    - [Chapter 2. The Structure of the Java Virtual Machine](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-2.html)
    - [Chapter 4. The class File Format](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-4.html)
    - [Chapter 6. The Java Virtual Machine Instruction Set](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html)

常用的字节码类库：

- [ASM](https://asm.ow2.io/)
- [BCEL](https://commons.apache.org/proper/commons-bcel/)
- [Byte Buddy](https://bytebuddy.net/)
- [Javassist](https://www.javassist.org/)

使用字节码进行代码分析：

- [Static Code Analysis in Java](https://www.baeldung.com/java-static-code-analysis-tutorial)