# springboot+maven多模块项目打包时找不到类（找不到符号）

[toc]

## 原理

当我们在使用springboot+maven构建多模块项目时，经常会用到两个插件：

maven-compiler-plugin和spring-boot-maven-plugin

当我们使用阿里云镜像构建springboot的时候默认在pom文件中这两个插件都会引入。但是如果多模块项目中同时使用这两个插件，则会产生找不到类的问题。

如果这两个插件同时存在会发生什么？

spring-boot-maven-plugin的执行在maven-compiler-plugin之后，我们执行打包命令后，会先由maven-compiler-plugin生成一个jar，之后再由spring-boot-maven-plugin生成一个同名的jar，因为是同名的，所以后面的会覆盖掉前面的，所以我们最终只能看到由spring-boot-maven-plugin生成的jar包。

为什么会找不到类？

这是由于spring-boot-maven-plugin生成的jar是fat jar，它的内部结构和maven-compiler-plugin生成的并不一样，classpath路径也不一样，所以如果一个项目是使用spring-boot-maven-plugin打的包，那么这个jar作为依赖包引入到其他项目中时，并不会像普通jar一样，可以在父项目中使用类路径找到某个类。

用大白话说就是，如果你的某个项目是要作为jar包依赖到其他项目中使用时，请不要使用spring-boot-maven-plugin，因为这样会让你的jar包内部结构变得与你想象的不一样。

我们应该怎么做？

比如我们有三个maven模块：common，dao，service，api。其中api是一个可以启动的springboot项目，其他模块都是作为jar包引入到api模块中使用的，这时候，我们只能在api模块中引入spring-boot-maven-plugin插件，将api项目打成可执行jar。其余项目都只使用maven-compiler-plugin。这样找不到类的问题就解决了。



## 举个例子

我们在项目主pom进行打包的时候主pom.xml 里面不要同时引入 maven-compiler-plugin 和 spring-boot-maven-plugin 这样会有问题。

正确的做法之一是仅仅在主pom.xml 中使用 maven-compiler-pulgin 而在非启动类模块中无需配置打包插件，在Spring Boot Application的启动类模块内引入spring-boot-maven-plugin 即可。

————————————————
版权声明：本文为CSDN博主「许三多fish」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/qq_36636154/article/details/109060638