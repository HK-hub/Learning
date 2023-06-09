# 17张图带你了解，JVM 运行时数据区

>  JVM 会把Java的字节码加载到运行时数据区内，这个内存区域分为：方法区、堆、虚拟机栈、本地方法栈以及程序计数器。

### 开篇

众所周知，Java程序的执行需要依赖于JVM(Java 虚拟机)。JVM 会将Java源代码编译成字节码文件，然后使用类加载器将其加载到运行时数据区中执行，垃圾收集器也会针对运行时数据区进行对象回收的工作。今天就来说说JVM的运行时数据区。

### 运行时数据区概述

在计算机世界中，内存是十分重要的系统资源，它承载着操作系统和应用程序实时运行的责任。JVM内存布局规定了Java在运行过程中内存申请、分配、管理的策略，从而保证了JVM的高效稳定运行。

Java虚拟机在执行Java程序的过程中，会将涉及到的数据划分到不同的内存区域去管理，在这些数据区域，有些是随着虚拟机启动而创建，虚拟机关闭而销毁。还有一部分是随着线程生命周期创建销毁的。这部分区域就是接下来要讲的Java虚拟机的运行时数据区。

![img](https://cdn.nlark.com/yuque/0/2022/png/12488211/1664416558148-7a9c97d8-c943-4f2f-aa49-8a60b9202b00.png)

图1 运行时数据区

如图1所示，红色的部分就是运行时数据区，它包括：方法区、堆、虚拟机栈、本地方法栈以及程序计数器五个部分。

图1中标注为黄色的方法区和堆是线程间共享的，也就是说它们会随着虚拟机启动而创建，随着虚拟机退出而销毁。橙色部分为每个线程单独享有的，即它们与线程是一一对应的，会随着线程开始和结束而创建和销毁。在HotSpot JVM中，每个线程都与操作系统的本地线程直接映射，例如：有一个Java线程准备好执行时，就有一个操作系统的本地线程被创建并且与Java 线程对应，当Java线程执行终止后，本地线程也会被回收。同时操作系统负责线程调度，及分配对应的CPU执行线程，一旦操作系统的本地线程初始化成功，它就会调用Java线程中的的run()方法去执行Java线程。

褐色部分的执行引擎就负责读取指令并且交由CPU执行，它包括解释器、JIT(即时编译器)，GC(垃圾回收器)。而另外一个褐色的本地库接口会提供Java程序调用的native方法。

另外，运行时数据区的划分也随着JDK的发展不断变迁，如图2 所示， JDK 1.6、JDK 1.7、JDK 1.8 的内存划分都会有所不同。

![img](https://cdn.nlark.com/yuque/0/2022/png/12488211/1664416593379-07d2fea0-e84b-468f-a3ae-4cf72792c8d4.png)
图2 运行时数据区的变迁

如图2 所示，在JDK 1.8 中加入了元数据区的概念，将原来保存在方法区中的运行时常量池和类常量池都包括其中。

### 虚拟机栈

上面介绍了JVM 运行时数据区的概念和组成，接下来一次介绍每个组成部分，首先从虚拟机栈开始。

每个Java线程都会对应一个虚拟机栈，换句话说多个线程就对应多个虚拟机栈。上面讲过了虚拟机栈是线程私有，虚拟机栈中包含多个栈帧(Stack Frame)，每一个栈帧是为方法执行而创建的，栈帧中描述的是Java方法执行的内存模型。每个方法从调用开始直到完成的全过程都对应着一个栈帧。栈帧是用来管理Java程序的运行，并保存方法的局部变量、部分结果、并参与方法的调用与返回。在活动线程中，只有一个栈帧是处于活跃状态的，也就是说只有位于栈顶的栈帧才是有效的，称为当前栈帧，与这个栈帧相关联的方法称为当前方法。执行引擎运行的所有字节码指令都只针对当前栈帧进行操作。

如图3 所示，每个Java 方法都会对应一个栈帧，左边的四个方法就对应了四个栈帧，从下往上依次是方法调用的顺序，最终方法1 会调用方法4， 此时正在执行方法4 ，它对应的栈帧4 就是“当前栈帧”，就是出于活跃状态的，其包含了局部变量表、操作数栈、动态链接以及返回地址等信息。
![img](https://cdn.nlark.com/yuque/0/2022/png/12488211/1664416635053-b9b08eba-b7c0-4cae-8d9c-5b15120ff3ca.png)图3 栈帧结构

#### 局部变量表

它定义为数字数组，主要用于存储方法参数和定义在方体内的局部变量，包含基本数据类型，对象引用，以及returnAddress类型。它建立在线程的栈上，是线程的私有数据，因此不存在数据的安全问题。

局部变量表所需的容量在编译期间确定，在运行期间是不改变其容量。方法嵌套调用的次数由栈的容量来决定，例如图3就进行了4个方法的嵌套，也就是说栈越大，方法嵌套调用次数越多。对一个函数而言，它的参数和局部变量越多，对应的栈帧就越大。因此，函数调用就会占用更多的栈空间。局部变量表中的变量只在当前方法调用中有效。在方法执行时，虚拟机通过使用局部变量表完成参数值到参数变量列表的传递。当方法调用结束后，随着方法栈帧的销毁，局部变量表也会随之销毁。



#### 操作数栈

它是一个后进先出的栈，在方法执行的过程中，根据字节码指令、往栈中写入或取出数据，即入栈/出栈。字节码指令将值压入操作栈，其余的字节码指令将操作数取出栈，进行操作之后再将结果压入栈。操作包括：复制、交换、求和等。

这样讲比较抽象，来看一个具体的例子。

如图4 所示，生成一个testAdd 方法，给变量i和j 分别赋值为1 和2 ，然后让其相加并且把结果赋值给k。

![img](https://cdn.nlark.com/yuque/0/2022/png/12488211/1664416723078-111aaaea-b3ab-4ae8-8f0a-f3dfeaf5da18.png)

图4 操作数栈代码

使用jclasslib反编译上面的代码得到图5 的结果。

![img](https://cdn.nlark.com/yuque/0/2022/png/12488211/1664416753597-08e9aaa8-de6c-40f9-9003-ddeb5c000121.png)

图5 jclasslib反编译结果



如图6 所示，当执行地址 0 的时候操作指令为bipush，此时程序寄存器的地址显示为0 ，bipush 命令将 1 压入到操作数栈的顶部。

![img](https://cdn.nlark.com/yuque/0/2022/png/12488211/1664416778237-84588b29-2328-4333-8017-78e4c3ca8919.png)

图6

如图7 所示，当指令地址到2 的时候，程序寄存器显示为2， 此时执行istore_1 的指令，将栈顶的数字1 保存到局部变量表中。

![img](https://cdn.nlark.com/yuque/0/2022/png/12488211/1664416802845-2cb4eac1-cae2-4fb3-818b-f323d3187f7b.png)

图7

如图8所示，指令地址执行到3 的时候，程序寄存器为3 ， bipush指令把2 压入到操作数栈的顶部。

![img](https://cdn.nlark.com/yuque/0/2022/png/12488211/1664416803241-af99a9aa-411a-498a-acb5-d22849626af8.png)



在指令地址为5 的时候，程序寄存器的值为5， istore_2指令将操作数栈中的2 保存到局部变量表中的2 的位置。

![img](https://cdn.nlark.com/yuque/0/2022/png/12488211/1664416835066-feb8ddf9-a94d-4e0b-a21d-657ed21cdaee.png)

图9

如图10所示，指令地址为6 的时候，执行iload_1 指令获取局部变量表中 位置为1 的值，也就是1 并且把它放到操作数栈的顶部。

![img](https://cdn.nlark.com/yuque/0/2022/png/12488211/1664416835139-f78da25d-dbcc-46b5-8411-52972ce005eb.png)

图10

如图11所示，指令地址为7 的时候，执行iload_2 指令，从局部变量表2 的位置取出值2 放到操作数栈的顶部。

![img](https://cdn.nlark.com/yuque/0/2022/png/12488211/1664416835165-6145cf94-3cdd-401b-b546-b24b2b00fd74.png)

图11

如图12 所示，在指令地址为8 时，执行iadd 指令，将操作数栈的两个数字1和2 相加结果为3，并且将其放到操作数栈的顶部。

![img](https://cdn.nlark.com/yuque/0/2022/png/12488211/1664416835400-87b193ad-bf10-4af4-a728-66a367dc8f58.png)

图12

如图13 所示，接着执行指令地址 9 ， istore_3 执行之后将操作数栈顶的3 保存到局部变量表3 的位置，完成相加的操作，最后通过指令地址10 中的return指令返回方法。

![img](https://cdn.nlark.com/yuque/0/2022/png/12488211/1664416835371-3363e30d-34d2-4c42-a13a-5a0ba3b36c90.png)

图13



#### 动态链接

在介绍动态链接之前先说说静态链接，即字节码文件被装载进JVM内部时，如果被调用的目标方法在编译期可知，且运行期间保持不变时。这种情况下将调用方法的符号引用转换为直接引用的过程称之为静态链接。但是，如果被调用方法在编译期间无法被确定下来，只能在程序运行时将调用方法的符号引用转换为直接引用，由于这种引用转换的过程具备动态性，被称为动态链接。

如图14所示，上面是反编译的字节码部分，对应的#3、#6、#5等等就是符号引用，下面的Constant pool就是常量池。在Java源文件被编译成字节码文件时，所有的变量和方法引用都作为符号引用保存在class文件的常量池中。例如在指令第9行会执行invokevirtual的指令，对应的符号引用就是#7，所对应常量池中的#7 就是Methodref，也就是方法引用，这里对应的方法是com.itcast.java.DynamicLinkTest中的methodA方法。

![img](https://cdn.nlark.com/yuque/0/2022/png/12488211/1664416888698-549ed0e0-4638-44a5-9b9b-b49d4d5f5bd9.png)

图14 从字节码到常量池中的方法引用

如图15所示，当字节码文件被加载后，字节码文件中的一些数据，如类型信息、域信息、方法信息等，就会被放置到方法区中。而栈帧中的当前类常量池引用(Current Class Constant Pool Reference)保存的是方法符号引用，真正的方法引用放在了方法区(Method Area)中的方法引用(method reference)中了，这个方法引用是为了支持代码的动态链接。动态链接就是将符号引用转化为直接引用。

![img](https://cdn.nlark.com/yuque/0/2022/png/12488211/1664416924421-317efb6b-d070-4aab-90f2-db3f44369bf8.png)

图15 栈帧中的当前类常量池引用对应方法区中的方法引用

JVM之所以这么设计是因为字节码文件需要数据支持的量会很大，因此不能直接将这些数据存放到字节码中。针对方法的引用创建符号引用，这个符号引用放在栈帧的常量池引用中，而实际的方法和符号引用的对照表却放在方法区的常量池中，这样字节码就可以通过常量池中的对照关系找到引用的方法，并且也不会增加栈帧的容量。

#### 方法返回地址

当一个方法开始执行后，可以通过两种方式退出该方法。第一种是执行引擎遇到方法返回的字节码指令，此时返回值会传递到上层调用者，这种方式称为正常完成出口。另外一种退出方式是在方法执行中遇到异常，这个异常在方法体内没有得到处理，就会导致方法退出，这种方式称为异常完成出口。由于是异常退出，就不会给上层调用者任何返回值。无论采取上面那种退出方式，方法都会到处调用它的位置，程序才能继续执行。方法在返回的时候需要在栈帧中保存一些信息，用来恢复调用该方法的上层方法的执行状态。这里可以通过方法调用者的程序计数器存放返回地址，如果是正常退出方法，上层方法会从程序计数器中保存的地址继续执行接下来的步骤。如果是异常退出的情况，返回地址就需要异常处理器来确定了。

### 程序计数器

有了上面虚拟机栈的讲解，对于程序计数器的理解会相对简单点。记得在虚拟机栈中的操作数栈的例子中，提到了使用程序计数器记录操作指令的地址。程序计数器就是一块较小的内存空间，它是当前线程执行的字节码的行号(操作指令的地址)指示器。在栈帧中字节码解释器就是通过改变计数器的值来选去下一条要执行的字节码指令的，例如：分支、循环、跳转、异常处理、线程恢复等。

上面讲虚拟机栈的时候提到过，多个执行的Java线程就是多个虚拟机栈，每个栈中存在多个栈帧，在一个时刻只有一个栈帧执行，也就是当前栈帧。也就是说在一个时刻一个处理只会对一个线程中的一个帧栈执行一条指令，而每个栈帧都会维护一个属于自己的程序计数器，这个计数器就是来记录指令执行的地址的。每个线程的计数器不会相互影响，这也保证了在Java 多线程进行切换的时候，每个线程都能够保证正确的指令地址被读取。

如图 16所示，在invokevirtual的框图中存在多个线程，每个线程就是一个虚拟机栈，每个线程中包含多个Frame 也就是栈帧，针对每个线程都会维护一个PC Registers也就是程序寄存器，它会记录指令地址信息，从而让方法实现：跳转、分支、循环、异常处理和线程恢复的功能。

![img](https://cdn.nlark.com/yuque/0/2022/png/12488211/1664424010982-b47da745-e8b3-4fd5-abf2-88f3fd136925.png)

图16 程序计数器



### 本地方法栈

本地方法栈与虚拟机栈所发挥的作用是非常相似的，它们之间的区别是虚拟机栈为虚拟机执行Java方法(也就是字节码)服务，而本地方法栈为虚拟机所使用到的Native方法服务。本地方法栈也会抛出StackOverflowError和OutOfMemoryError异常。

说白了，本地方法(Native Method)就是一个Java调用非Java代码的接口。 当Java应用需要与Java之外的环境交互时就需要使用本地方法，特别与底层系统、操作系统以及硬件打交道时就会用到本地方法。大家可以把本地方法理解为一种交流机制：它提供了一个对外的简洁的接口，让我们无需去了解Java应用之外的细节。

那么JVM是如何使用Native Method的呢?当一个类第一次被使用时，类的字节码会被加载到内存，在字节码的入口维持着该类所有方法描述符的list，包括：方法代码来源，参数，方法描述符(例如：public)等等。

如果方法描述符是native，同时描述符块将有一个指向该方法实现的指针，而具体实现在DLL文件内，此时DLL文件会被操作系统加载到Java程序的地址空间里。当一个带有本地方法的类被加载时，其相关的DLL并未被加载，因此指向方法实现的指针并不会被设置。当本地方法被调用之前， DLL才会被加载，即通过调用java.system.loadLibrary()实现的。



### 堆和方法区

上面说的虚拟机栈、程序计数器和本地方法栈都是线程私有的，而接下来说的方法区和堆是线程共享的。这里把堆和方法区合起来说。

#### 堆

Java堆是Java虚拟机所管理内存中最大的一块，在虚拟机启动时创建，被所有线程共享。Java对象实例以及数组都在堆上分配。堆的大小可以是固定的，也可以根据计算的需要进行扩展，如果不需要更大的堆，则可以收缩。堆的内存不需要是连续的。Java虚拟机实现可以为程序员或用户提供对堆初始大小的控制，如果可以动态扩展或收缩堆，还可以控制堆的最大和最小大小。

Java堆是垃圾收集器管理的主要区域，所以也被称为GC堆。从内存回收的角度来看，由于现在收集器基本都采用分代收集算法，所以Java堆中还可以细分为：新生代和老年代;新生代再细分就是：Eden空间、From Survivor空间、ToSurvivor空间等。从内存分配的角度来看，线程共享的Java堆中可能划分出多个线程私有的分配缓冲区(Thread Local Allocation Buffer，TLAB)。不论如何划分，都与存放内容无关，无论哪个区域，存放的都仍然是对象实例;进一步划分的目的是为了更好的回收内存，或者更快地分配内存。

对于堆中垃圾回收的部分这里不展开说明，后面会有文章去介绍。



#### 方法区

方法区和堆一样是线程共享的内存区域，它用来存放被虚拟机加载的类型信息、运行时常量池、静态变量、JIT代码缓存、域信息、方法信息等。方法区(Method Area)与Java堆一样，是各个线程共享的内存区域，有如下特点：

- - 方法区在JVM启动的时候被创建，并且它的实际的物理内存空间和Java堆区一样都可以是不连续的。
    - 方法区的大小，和堆空间一样，可以选择固定大小和可扩展。
    - 方法区的大小决定了系统可以保存多少个类，如果系统定义了太多的类，导致方法区溢出，虚拟机就会抛出内存溢出错误：

java.lang.OutOfMemoryError:PermGenspace或者 java.lang.OutOfMemoryError: Metaspace。

- - 关闭JVM就会释放这个区域的内存。

这里把堆、方法区和虚拟机栈的关系整理一下。如图17 所示，在右边创建了AppMain 类，在运行时JVM 会把AppMain的信息放入到方法区，因为方法区会存放类型信息。同时main 的方法本身也会放入到方法区。接下来的new Sample(“测试1”)的语句中Sample的自定义对象会放到堆里面，而对应的test1 应用会放入到虚拟机栈中，对应的test1.printName()方法的执行会在虚拟机栈中的栈帧中通过指令执行完成。另外下面的class Sample也是放到方法区中的，声明的private name，其中name的引用放在虚拟机栈中，name对应的对象放在堆中。对应的printName方法是放在方法区中的。


![img](https://cdn.nlark.com/yuque/0/2022/png/12488211/1664424148337-36878d97-296b-460d-abfc-c88c669b5966.png)

图17 栈、堆、方法区关系

### 总结

JVM 会把Java的字节码加载到运行时数据区内，这个内存区域分为：方法区、堆、虚拟机栈、本地方法栈以及程序计数器。堆里面放对象，也是垃圾回收器要处理的对象;方法区放类型、方法描述、方法本体;程序计数器负责记录虚拟机栈中指令执行的地址;虚拟机栈对应Java执行的线程，对象的引用都保存在栈帧中，通过指令地址和指令执行方法中的内容;本地方法栈用来调用Java 之外的系统级别的接口。