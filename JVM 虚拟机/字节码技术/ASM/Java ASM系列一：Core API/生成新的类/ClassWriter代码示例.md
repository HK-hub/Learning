# ClassWriter代码示例

[toc]

![](https://lsieun.github.io/assets/images/java/asm/what-asm-can-do.png)

在当前阶段，我们只能进行Class Generation的操作。

## 1. 示例一：生成接口

### 1.1. 预期目标

我们的预期目标：生成`HelloWorld`接口。

```java
public interface HelloWorld {
}
```

### 1.2. 编码实现

```java
import lsieun.utils.FileUtils;
import org.objectweb.asm.ClassWriter;

import static org.objectweb.asm.Opcodes.*;

public class HelloWorldGenerateCore {
    public static void main(String[] args) throws Exception {
        String relative_path = "sample/HelloWorld.class";
        String filepath = FileUtils.getFilePath(relative_path);

        // (1) 生成byte[]内容
        byte[] bytes = dump();

        // (2) 保存byte[]到文件
        FileUtils.writeBytes(filepath, bytes);
    }

    public static byte[] dump() throws Exception {
        // (1) 创建ClassWriter对象
        ClassWriter cw = new ClassWriter(ClassWriter.COMPUTE_FRAMES);

        // (2) 调用visitXxx()方法
        cw.visit(
                V1_8,                                        // version
                ACC_PUBLIC + ACC_ABSTRACT + ACC_INTERFACE,   // access
                "sample/HelloWorld",                         // name
                null,                                        // signature
                "java/lang/Object",                          // superName
                null                                         // interfaces
        );

        cw.visitEnd();

        // (3) 调用toByteArray()方法
        return cw.toByteArray();
    }
}
```

在上述代码中，我们调用了`visit()`方法、`visitEnd()`方法和`toByteArray()`方法。

由于`sample.HelloWorld`这个接口中，并没有定义任何的字段和方法，因此，在上述代码中没有调用`visitField()`方法和`visitMethod()`方法。

### 1.3. 验证结果

```java
public class HelloWorldRun {
    public static void main(String[] args) throws Exception {
        Class<?> clazz = Class.forName("sample.HelloWorld");
        System.out.println(clazz);
    }
}
```

### 1.4. 小总结

#### 1.4.1. visit()方法

在这里，我们重点介绍一下`visit(version, access, name, signature, superName, interfaces)`方法的各个参数：

- `version`: 表示当前类的版本信息。在上述示例代码中，其取值为`Opcodes.V1_8`，表示使用Java 8版本。
- `access`: 表示当前类的访问标识（access flag）信息。在上面的示例中，`access`的取值是`ACC_PUBLIC + ACC_ABSTRACT + ACC_INTERFACE`，也可以写成`ACC_PUBLIC | ACC_ABSTRACT | ACC_INTERFACE`。如果想进一步了解这些标识的含义，可以参考[Java Virtual Machine Specification](https://docs.oracle.com/javase/specs/jvms/se8/html/index.html)的Chapter 4. The class File Format部分。
- `name`: 表示当前类的名字，它采用的格式是Internal Name的形式。
- `signature`: 表示当前类的泛型信息。因为在这个接口当中不包含任何的泛型信息，因此它的值为`null`。
- `superName`: 表示当前类的父类信息，它采用的格式是Internal Name的形式。
- `interfaces`: 表示当前类实现了哪些接口信息。

#### 1.4.2. Internal Name

同时，我们也要介绍一下Internal Name的概念：在`.java`文件中，我们使用Java语言来编写代码，使用类名的形式是**Fully Qualified Class Name**，例如`java.lang.String`；将`.java`文件编译之后，就会生成`.class`文件；在`.class`文件中，类名的形式会发生变化，称之为**Internal Name**，例如`java/lang/String`。因此，将**Fully Qualified Class Name**转换成**Internal Name**的方式就是，将`.`字符转换成`/`字符。

|          | Java Language              | Java ClassFile     |
| -------- | -------------------------- | ------------------ |
| 文件格式 | `.java`                    | `.class`           |
| 类名     | Fully Qualified Class Name | Internal Name      |
| 类名示例 | `java.lang.String`         | `java/lang/String` |

## 2. 示例二：生成接口+字段+方法

### 2.1. 预期目标

我们的预期目标：生成`HelloWorld`接口。

```java
public interface HelloWorld extends Cloneable {
    int LESS = -1;
    int EQUAL = 0;
    int GREATER = 1;
    int compareTo(Object o);
}
```

### 2.2. 编码实现

```java
import lsieun.utils.FileUtils;
import org.objectweb.asm.ClassWriter;
import org.objectweb.asm.FieldVisitor;
import org.objectweb.asm.MethodVisitor;

import static org.objectweb.asm.Opcodes.*;

public class HelloWorldGenerateCore {
    public static void main(String[] args) throws Exception {
        String relative_path = "sample/HelloWorld.class";
        String filepath = FileUtils.getFilePath(relative_path);

        // (1) 生成byte[]内容
        byte[] bytes = dump();

        // (2) 保存byte[]到文件
        FileUtils.writeBytes(filepath, bytes);
    }

    public static byte[] dump() throws Exception {
        // (1) 创建ClassWriter对象
        ClassWriter cw = new ClassWriter(ClassWriter.COMPUTE_FRAMES);

        // (2) 调用visitXxx()方法
        cw.visit(V1_8, ACC_PUBLIC + ACC_ABSTRACT + ACC_INTERFACE, "sample/HelloWorld",
                null, "java/lang/Object", new String[]{"java/lang/Cloneable"});

        {
            FieldVisitor fv1 = cw.visitField(ACC_PUBLIC + ACC_FINAL + ACC_STATIC, "LESS", "I", null, -1);
            fv1.visitEnd();
        }

        {
            FieldVisitor fv2 = cw.visitField(ACC_PUBLIC + ACC_FINAL + ACC_STATIC, "EQUAL", "I", null, 0);
            fv2.visitEnd();
        }

        {
            FieldVisitor fv3 = cw.visitField(ACC_PUBLIC + ACC_FINAL + ACC_STATIC, "GREATER", "I", null, 1);
            fv3.visitEnd();
        }

        {
            MethodVisitor mv1 = cw.visitMethod(ACC_PUBLIC + ACC_ABSTRACT, "compareTo", "(Ljava/lang/Object;)I", null, null);
            mv1.visitEnd();
        }


        cw.visitEnd();

        // (3) 调用toByteArray()方法
        return cw.toByteArray();
    }
}
```

在上述代码中，我们调用了`visit()`方法、`visitField()`方法、`visitMethod()`方法、`visitEnd()`方法和`toByteArray()`方法。

### 2.3. 验证结果

```java
import java.lang.reflect.Field;
import java.lang.reflect.Method;

public class HelloWorldRun {
    public static void main(String[] args) throws Exception {
        Class<?> clazz = Class.forName("sample.HelloWorld");
        Field[] declaredFields = clazz.getDeclaredFields();
        if (declaredFields.length > 0) {
            System.out.println("fields:");
            for (Field f : declaredFields) {
                System.out.println("    " + f.getName());
            }
        }

        Method[] declaredMethods = clazz.getDeclaredMethods();
        if (declaredMethods.length > 0) {
            System.out.println("methods:");
            for (Method m : declaredMethods) {
                System.out.println("    " + m.getName());
            }
        }
    }
}
```

输出结果：

```
fields:
    LESS
    EQUAL
    GREATER
methods:
    compareTo
```

### 2.4. 小总结

#### 2.4.1. visitField()和visitMethod()方法

在这里，我们重点说一下`visitField()`方法和`visitMethod()`方法的各个参数：

- `visitField (access, name, descriptor, signature, value)`
- `visitMethod(access, name, descriptor, signature, exceptions)`

这两个方法的前4个参数是相同的，不同的地方只在于第5个参数。

- `access`参数：表示当前字段或方法带有的访问标识（access flag）信息，例如`ACC_PUBLIC`、`ACC_STATIC`和`ACC_FINAL`等。
- `name`参数：表示当前字段或方法的名字。
- `descriptor`参数：表示当前字段或方法的描述符。这些描述符，与我们平时使用的Java类型是有区别的。
- `signature`参数：表示当前字段或方法是否带有泛型信息。换句话说，如果不带有泛型信息，提供一个`null`就可以了；如果带有泛型信息，就需要给它提供某一个具体的值。
- `value`参数：是`visitField()`方法的第5个参数。这个参数的取值，与当前字段是否为常量有关系。如果当前字段是一个常量，就需要给`value`参数提供某一个具体的值；如果当前字段不是常量，那么使用`null`就可以了。
- `exceptions`参数：是`visitMethod()`方法的第5个参数。这个参数的取值，与当前方法头（Method Header）中是否具有`throws XxxException`相关。

我们可以使用`PrintASMCodeCore`类来查看下面的`sample.HelloWorld`类的ASM代码，从而观察`value`参数和`exceptions`参数的取值：

```java
import java.io.FileNotFoundException;
import java.io.IOException;

public class HelloWorld {
    // 这是一个常量字段，使用static、final关键字修饰
    public static final int constant_field = 10;
    // 这是一个非常量字段
    public int non_constant_field;

    public void test() throws FileNotFoundException, IOException {
        // do nothing
    }
}
```

对于上面的代码，

- `constant_field`字段：对应于`visitField(ACC_PUBLIC | ACC_FINAL | ACC_STATIC, "constant_field", "I", null, new Integer(10))`
- `non_constant_field`字段：对应于`visitField(ACC_PUBLIC, "non_constant_field", "I", null, null)`
- `test()`方法：对应于`visitMethod(ACC_PUBLIC, "test", "()V", null, new String[] { "java/io/FileNotFoundException", "java/io/IOException" })`

#### 2.4.2. 描述符（descriptor）

在ClassFile当中，描述符（descriptor）是对“类型”的简单化描述。

- 对于字段（field）来说，描述符（descriptor）就是对**字段本身的类型**进行简单化描述。
- 对于方法（method）来说，描述符（descriptor）就是对方法的**接收参数的类型**和**返回值的类型**进行简单化描述。

| Java类型              | ClassFile描述符                                 |
| --------------------- | ----------------------------------------------- |
| `boolean`             | `Z`（Z表示Zero，零表示`false`，非零表示`true`） |
| `byte`                | `B`                                             |
| `char`                | `C`                                             |
| `double`              | `D`                                             |
| `float`               | `F`                                             |
| `int`                 | `I`                                             |
| `long`                | `J`                                             |
| `short`               | `S`                                             |
| `void`                | `V`                                             |
| `non-array reference` | `L<InternalName>;`                              |
| `array reference`     | `[`                                             |

对字段描述符的举例：

- `boolean flag`: `Z`
- `byte byteValue`: `B`
- `int intValue`: `I`
- `float floatValue`: `F`
- `double doubleValue`: `D`
- `String strValue`: `Ljava/lang/String;`
- `Object objValue`: `Ljava/lang/Object;`
- `byte[] bytes`: `[B`
- `String[] array`: `[Ljava/lang/String;`
- `Object[][] twoDimArray`: `[[Ljava/lang/Object;`

对方法描述符的举例：

- `int add(int a, int b)`: `(II)I`
- `void test(int a, int b)`: `(II)V`
- `boolean compare(Object obj)`: `(Ljava/lang/Object;)Z`
- `void main(String[] args)`: `([Ljava/lang/String;)V`

## 3. 示例三：生成类

### 3.1. 预期目标

我们的预期目标：生成`HelloWorld`类。

```
public class HelloWorld {
}
```

### 3.2. 编码实现

```java
import lsieun.utils.FileUtils;
import org.objectweb.asm.*;

import static org.objectweb.asm.Opcodes.*;

public class HelloWorldGenerateCore {
    public static void main(String[] args) throws Exception {
        String relative_path = "sample/HelloWorld.class";
        String filepath = FileUtils.getFilePath(relative_path);

        // (1) 生成byte[]内容
        byte[] bytes = dump();

        // (2) 保存byte[]到文件
        FileUtils.writeBytes(filepath, bytes);
    }

    public static byte[] dump() throws Exception {
        // (1) 创建ClassWriter对象
        ClassWriter cw = new ClassWriter(ClassWriter.COMPUTE_FRAMES);

        // (2) 调用visitXxx()方法
        cw.visit(V1_8, ACC_PUBLIC + ACC_SUPER, "sample/HelloWorld",
                null, "java/lang/Object", null);

        MethodVisitor mv = cw.visitMethod(ACC_PUBLIC, "<init>", "()V", null, null);
        mv.visitCode();
        mv.visitVarInsn(ALOAD, 0);
        mv.visitMethodInsn(INVOKESPECIAL, "java/lang/Object", "<init>", "()V", false);
        mv.visitInsn(RETURN);
        mv.visitMaxs(1, 1);
        mv.visitEnd();

        cw.visitEnd();

        // (3) 调用toByteArray()方法
        return cw.toByteArray();
    }
}
```

### 3.3. 验证结果

```java
public class HelloWorldRun {
    public static void main(String[] args) throws Exception {
        Class<?> clazz = Class.forName("sample.HelloWorld");
        System.out.println(clazz);
    }
}
```

### 3.4. **<init>**()和<clinit>()方法

对于一个类（Class）来说，如果没有提供任何构造方法，Java编译器会自动生成一个默认构造方法。在所有的`.class`文件中，构造方法的名字是`<init>()`。

另外，如果在`.class`文件中包含静态代码块，那么就会有一个`<clinit>()`方法。

```java
package sample;

public class HelloWorld {
    static {
        System.out.println("static code block");
    }
}
```

上面的静态代码码，对应于`visitMethod(ACC_STATIC, "<clinit>", "()V", null, null)`的调用。

## 4. 总结

本文主要对`ClassWriter`类的代码示例进行介绍，主要目的是希望大家能够对`ClassWriter`类熟悉起来。

本文内容总结如下：

- 第一点，我们需要注意`ClassWriter`/`ClassVisitor`中`visit()`、`visitField()`、`visitMethod()`和`visitEnd()`方法的调用顺序。
- 第二点，我们对于`visit()`方法、`visitField()`方法和`visitMethod()`方法接收的参数进行了介绍。虽然我们并没有特别介绍`visitEnd()`方法和`toByteArray()`方法，并不表示这两个方法不重要，只是因为这两个方法不接收任何参数。
- 第三点，我们介绍了Internal Name和Descriptor（描述符）这两个概念，在使用时候需要加以注意，因为它们与我们在使用Java语言编写代码时是不一样的。
- 第四点，在`.class`文件中，构造方法的名字是`<init>()`，表示instance initialization method；静态代码块的名字是`<clinit>()`，表示class initialization method。

另外，`visitField()`方法会返回一个`FieldVisitor`对象，而`visitMethod()`方法会返回一个`MethodVisitor`对象；在后续的内容当中，我们会分别介绍`FieldVisitor`类和`MethodVisitor`类。