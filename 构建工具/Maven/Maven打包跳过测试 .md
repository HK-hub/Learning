# [Maven打包跳过测试 ](https://www.cnblogs.com/three-fighter/p/13996864.html)

使用Maven打包的时候，可能会因为单元测试打包失败，这时候就需要跳过单元测试。

Maven跳过单元测试有三种方法。

# 一、命令行方式跳过测试

我们可以用两种命令来跳过测试

- mvn clean install -DskipTests
- mvn clean install -Dmaven.test.skip=true

\- DskipTests，不执行测试用例，但编译测试用例类生成相应的class文件至target/test-classes下

\- Dmaven.test.skip=true，不执行测试用例，也不编译测试用例类

使用maven.test.skip，不但跳过单元测试的运行，也跳过测试代码的编译;

使用 mvn package -DskipTests 跳过单元测试，但是会继续编译。



# 二、pom.xml中配置跳过测试

可以在pom.xml中添加如下配置来跳过测试：

```java
<build>
  <plugins>
<!-- maven 打包时跳过测试 -->
			<plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <configuration>
                    <skip>true</skip>
                </configuration>
            </plugin>
       </plugins>
   <build>         
```



# 三、Idea中配置跳过测试

Idea是我们常用的开发工具，我们经常直接用Idea的插件来进行打包。我们可以通过配置来跳过测试。

## 1、直接配置

Maven命令栏的工具栏有下图中的图标，这个图标就是 Skip Tests。点击选中，再用LifeStyle中的打包就会跳过测试.

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201117215446213.png?#pic_center)



## 2、更改Maven配置

- 方式1：打开配置，找到Maven-->Runner，在VM option中添加 -Dmaven.test.skip=true

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201117220025453.png?#pic_center)



- 方式2：Runner--> Propertis 中选中 Skip tests

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201117220244332.png?#pic_center)





**参考：**

【1】：[Maven 两种方式跳过编译 test](https://lihuimintu.github.io/2019/12/14/Maven-SkipTest/)
【2】：[IDEA中Maven打包时如何跳过测试](https://my.oschina.net/u/3866531/blog/2250997)
【3】：[mvn打包项目时跳过测试的两种方法](https://blog.csdn.net/chen15369337607/article/details/78422693)
【4】：[Idea中配置Maven跳过测试编译操作](https://blog.csdn.net/m0_37779570/article/details/81063729)