# Java 根据元素字段或属性去重List 集合

本例子使用的[实体类](https://so.csdn.net/so/search?q=实体类&spm=1001.2101.3001.7020)如下：

```java
class Person {
    private String name;

    private int age;

    //省略getter/setter/toString
}
```

第一种去重方式：java8利用Collectors.toMap去重

```java
        List<Person> personList = new ArrayList<>();



        personList.add(new Person("one", 10));



        personList.add(new Person("two", 20));



        personList.add(new Person("three", 30));



        //这两的name值重复



        personList.add(new Person("four", 40));



        personList.add(new Person("four", 45));



 



        System.out.println("利用Collectors.toMap去重:");



        //利用Collectors.toMap去重



        personList.stream()



                .collect(Collectors.toMap(Person::getName, Function.identity(), (oldValue, newValue) -> oldValue))



                .values()



                .stream()



                .forEach(System.out::println); //打印
```

   输出如下： 

```java
利用Collectors.toMap去重:



Person{name='four', age=40}



Person{name='one', age=10}



Person{name='three', age=30}



Person{name='two', age=20}
```

   其中，Collectors.toMap需要使用三个参数的版本，前两个参数一个是keyMapper函数一个是valueMapper函数的，用过toMap的都知道，去重的关键在于第三个参数BinaryOperator函数接口。

​    这个BinaryOperator函数接收两个参数，如上面代码，一个oldValue，一个newValue，字面意思，第一个旧值，第二个是新值。当stream构造Map时，会先调用Map的get方法获取该key对应节点的旧值，如果该值为null，则不会调用BinaryOperator函数，如果不为null，则会把获取的旧值与新值作为参数传给函数执行，然后把函数的返回值作为新值put到Map中。如果看不懂，请看源码吧：

![img](https://img-blog.csdnimg.cn/2019041014571618.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2OTUxMTE2,size_16,color_FFFFFF,t_70)

第二种去重方式：利用Collectors.toCollection和TreeSet去重:

```java
 List<Person> personList = new ArrayList<>();



        personList.add(new Person("one", 10));



        personList.add(new Person("two", 20));



        personList.add(new Person("three", 30));



        //这两的name值重复



        personList.add(new Person("four", 40));



        personList.add(new Person("four", 42));



 



        System.out.println("利用Collectors.toCollection和TreeSet去重:");



        //利用TreeSet原理去重(TreeSet内部使用的是TreeMap，使用指定Comparator比较元素，如果元素相同，则新元素代替旧元素)



        personList.stream()



                .collect(Collectors.toCollection(() -> new TreeSet<>(Comparator.comparing(Person::getName))))



                .stream()



                .forEach(System.out::println); //打印
```

注释中也说了，利用TreeSet原理去重，TreeSet内部使用的是TreeMap，使用指定Comparator比较元素，如果元素相同，则新元素代替旧元素，TreeMap的该部分源码如下图：

如果不想要返回TreeSet类型，那也可以使用Collectors.collectingAndThen转换成ArrayList，也可以用new ArrayList(set)，原理一样，如下：

```coffeescript
List<Person> distinctList = personList.stream()



                .collect(Collectors.collectingAndThen(Collectors.toCollection(() -> new TreeSet<>(Comparator.comparing(Person::getName))), ArrayList::new));
```

 ![img](https://img-blog.csdnimg.cn/20190410150406264.png)

不是java8咋办？还能咋办？上面的源码都给出了。。copy源码自己封装呗。。利用map和treeSet的原理而已

