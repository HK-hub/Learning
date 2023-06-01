# Label代码示例

[toc]

## 1. 示例一：if语句

### 1.1. 预期目标

```java
public class HelloWorld {
    public void test(int value) {
        if (value == 0) {
            System.out.println("value is 0");
        }
        else {
            System.out.println("value is not 0");
        }
    }
}
```

### 1.2. 编码实现

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

        {
            MethodVisitor mv1 = cw.visitMethod(ACC_PUBLIC, "<init>", "()V", null, null);
            mv1.visitCode();
            mv1.visitVarInsn(ALOAD, 0);
            mv1.visitMethodInsn(INVOKESPECIAL, "java/lang/Object", "<init>", "()V", false);
            mv1.visitInsn(RETURN);
            mv1.visitMaxs(0, 0);
            mv1.visitEnd();
        }

        {
            MethodVisitor mv2 = cw.visitMethod(ACC_PUBLIC, "test", "(I)V", null, null);
            Label elseLabel = new Label();
            Label returnLabel = new Label();

            // 第1段
            mv2.visitCode();
            mv2.visitVarInsn(ILOAD, 1);
            mv2.visitJumpInsn(IFNE, elseLabel);
            mv2.visitFieldInsn(GETSTATIC, "java/lang/System", "out", "Ljava/io/PrintStream;");
            mv2.visitLdcInsn("value is 0");
            mv2.visitMethodInsn(INVOKEVIRTUAL, "java/io/PrintStream", "println", "(Ljava/lang/String;)V", false);
            mv2.visitJumpInsn(GOTO, returnLabel);

            // 第2段
            mv2.visitLabel(elseLabel);
            mv2.visitFieldInsn(GETSTATIC, "java/lang/System", "out", "Ljava/io/PrintStream;");
            mv2.visitLdcInsn("value is not 0");
            mv2.visitMethodInsn(INVOKEVIRTUAL, "java/io/PrintStream", "println", "(Ljava/lang/String;)V", false);

            // 第3段
            mv2.visitLabel(returnLabel);
            mv2.visitInsn(RETURN);
            mv2.visitMaxs(0, 0);
            mv2.visitEnd();
        }

        cw.visitEnd();

        // (3) 调用toByteArray()方法
        return cw.toByteArray();
    }
}
```

### 1.3. 验证结果

```java
import java.lang.reflect.Method;

public class HelloWorldRun {
    public static void main(String[] args) throws Exception {
        Class<?> clazz = Class.forName("sample.HelloWorld");
        Object obj = clazz.newInstance();

        Method method = clazz.getDeclaredMethod("test", int.class);
        method.invoke(obj, 0);
        method.invoke(obj, 1);
    }
}
```

### 1.4. 小总结

通过上面的示例，我们注意三个知识点：

- 第一点，如何使用`ClassWriter`类。
- 第二点，在使用`MethodVisitor`类时，其中`visitXxx()`方法需要遵循的调用顺序。
- 第三点，如何通过`Label`类来实现if语句。

## 2. 示例二：switch语句

从Instruction的角度来说，实现switch语句可以使用`lookupswitch`或`tableswitch`指令。

### 2.1. 预期目标

```java
public class HelloWorld {
    public void test(int val) {
        switch (val) {
            case 1:
                System.out.println("val = 1");
                break;
            case 2:
                System.out.println("val = 2");
                break;
            case 3:
                System.out.println("val = 3");
                break;
            case 4:
                System.out.println("val = 4");
                break;
            default:
                System.out.println("val is unknown");
        }
    }
}
```

### 2.2. 编码实现

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

        {
            MethodVisitor mv1 = cw.visitMethod(ACC_PUBLIC, "<init>", "()V", null, null);
            mv1.visitCode();
            mv1.visitVarInsn(ALOAD, 0);
            mv1.visitMethodInsn(INVOKESPECIAL, "java/lang/Object", "<init>", "()V", false);
            mv1.visitInsn(RETURN);
            mv1.visitMaxs(0, 0);
            mv1.visitEnd();
        }

        {
            MethodVisitor mv2 = cw.visitMethod(ACC_PUBLIC, "test", "(I)V", null, null);
            Label caseLabel1 = new Label();
            Label caseLabel2 = new Label();
            Label caseLabel3 = new Label();
            Label caseLabel4 = new Label();
            Label defaultLabel = new Label();
            Label returnLabel = new Label();

            // 第1段
            mv2.visitCode();
            mv2.visitVarInsn(ILOAD, 1);
            mv2.visitTableSwitchInsn(1, 4, defaultLabel, new Label[]{caseLabel1, caseLabel2, caseLabel3, caseLabel4});

            // 第2段
            mv2.visitLabel(caseLabel1);
            mv2.visitFieldInsn(GETSTATIC, "java/lang/System", "out", "Ljava/io/PrintStream;");
            mv2.visitLdcInsn("val = 1");
            mv2.visitMethodInsn(INVOKEVIRTUAL, "java/io/PrintStream", "println", "(Ljava/lang/String;)V", false);
            mv2.visitJumpInsn(GOTO, returnLabel);

            // 第3段
            mv2.visitLabel(caseLabel2);
            mv2.visitFieldInsn(GETSTATIC, "java/lang/System", "out", "Ljava/io/PrintStream;");
            mv2.visitLdcInsn("val = 2");
            mv2.visitMethodInsn(INVOKEVIRTUAL, "java/io/PrintStream", "println", "(Ljava/lang/String;)V", false);
            mv2.visitJumpInsn(GOTO, returnLabel);

            // 第4段
            mv2.visitLabel(caseLabel3);
            mv2.visitFieldInsn(GETSTATIC, "java/lang/System", "out", "Ljava/io/PrintStream;");
            mv2.visitLdcInsn("val = 3");
            mv2.visitMethodInsn(INVOKEVIRTUAL, "java/io/PrintStream", "println", "(Ljava/lang/String;)V", false);
            mv2.visitJumpInsn(GOTO, returnLabel);

            // 第5段
            mv2.visitLabel(caseLabel4);
            mv2.visitFieldInsn(GETSTATIC, "java/lang/System", "out", "Ljava/io/PrintStream;");
            mv2.visitLdcInsn("val = 4");
            mv2.visitMethodInsn(INVOKEVIRTUAL, "java/io/PrintStream", "println", "(Ljava/lang/String;)V", false);
            mv2.visitJumpInsn(GOTO, returnLabel);

            // 第6段
            mv2.visitLabel(defaultLabel);
            mv2.visitFieldInsn(GETSTATIC, "java/lang/System", "out", "Ljava/io/PrintStream;");
            mv2.visitLdcInsn("val is unknown");
            mv2.visitMethodInsn(INVOKEVIRTUAL, "java/io/PrintStream", "println", "(Ljava/lang/String;)V", false);

            // 第7段
            mv2.visitLabel(returnLabel);
            mv2.visitInsn(RETURN);
            mv2.visitMaxs(0, 0);
            mv2.visitEnd();
        }

        cw.visitEnd();

        // (3) 调用toByteArray()方法
        return cw.toByteArray();
    }
}
```

### 2.3. 验证结果

```java
import java.lang.reflect.Method;

public class HelloWorldRun {
    public static void main(String[] args) throws Exception {
        Class<?> clazz = Class.forName("sample.HelloWorld");
        Object obj = clazz.newInstance();

        Method method = clazz.getDeclaredMethod("test", int.class);
        for (int i = 1; i < 6; i++) {
            method.invoke(obj, i);
        }
    }
}
```

### 2.4. 小总结

通过上面的示例，我们注意三个知识点：

- 第一点，如何使用`ClassWriter`类。
- 第二点，在使用`MethodVisitor`类时，其中`visitXxx()`方法需要遵循的调用顺序。
- 第三点，如何通过`Label`类来实现switch语句。在本示例当中，使用了`MethodVisitor.visitTableSwitchInsn()`方法，也可以使用`MethodVisitor.visitLookupSwitchInsn()`方法。

## 3. 示例三：for语句

### 3.1. 预期目标

```java
public class HelloWorld {
    public void test() {
        for (int i = 0; i < 10; i++) {
            System.out.println(i);
        }
    }
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

        {
            MethodVisitor mv1 = cw.visitMethod(ACC_PUBLIC, "<init>", "()V", null, null);
            mv1.visitCode();
            mv1.visitVarInsn(ALOAD, 0);
            mv1.visitMethodInsn(INVOKESPECIAL, "java/lang/Object", "<init>", "()V", false);
            mv1.visitInsn(RETURN);
            mv1.visitMaxs(0, 0);
            mv1.visitEnd();
        }

        {
            MethodVisitor methodVisitor = cw.visitMethod(ACC_PUBLIC, "test", "()V", null, null);
            Label conditionLabel = new Label();
            Label returnLabel = new Label();

            // 第1段
            methodVisitor.visitCode();
            methodVisitor.visitInsn(ICONST_0);
            methodVisitor.visitVarInsn(ISTORE, 1);

            // 第2段
            methodVisitor.visitLabel(conditionLabel);
            methodVisitor.visitVarInsn(ILOAD, 1);
            methodVisitor.visitIntInsn(BIPUSH, 10);
            methodVisitor.visitJumpInsn(IF_ICMPGE, returnLabel);
            methodVisitor.visitFieldInsn(GETSTATIC, "java/lang/System", "out", "Ljava/io/PrintStream;");
            methodVisitor.visitVarInsn(ILOAD, 1);
            methodVisitor.visitMethodInsn(INVOKEVIRTUAL, "java/io/PrintStream", "println", "(I)V", false);
            methodVisitor.visitIincInsn(1, 1);
            methodVisitor.visitJumpInsn(GOTO, conditionLabel);

            // 第3段
            methodVisitor.visitLabel(returnLabel);
            methodVisitor.visitInsn(RETURN);
            methodVisitor.visitMaxs(0, 0);
            methodVisitor.visitEnd();
        }

        cw.visitEnd();

        // (3) 调用toByteArray()方法
        return cw.toByteArray();
    }
}
```

### 3.3. 验证结果

```java
import java.lang.reflect.Method;

public class HelloWorldRun {
    public static void main(String[] args) throws Exception {
        Class<?> clazz = Class.forName("sample.HelloWorld");
        Object obj = clazz.newInstance();

        Method method = clazz.getDeclaredMethod("test");
        method.invoke(obj);
    }
}
```

### 3.4. 小总结

通过上面的示例，我们注意三个知识点：

- 第一点，如何使用`ClassWriter`类。
- 第二点，在使用`MethodVisitor`类时，其中`visitXxx()`方法需要遵循的调用顺序。
- 第三点，如何通过`Label`类来实现for语句。

## 4. 示例四：try-catch语句

### 4.1. 预期目标

```java
public class HelloWorld {
    public void test() {
        try {
            System.out.println("Before Sleep");
            Thread.sleep(1000);
            System.out.println("After Sleep");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

### 4.2. 编码实现

```java
import lsieun.utils.FileUtils;
import org.objectweb.asm.ClassWriter;
import org.objectweb.asm.Label;
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
        cw.visit(V1_8, ACC_PUBLIC + ACC_SUPER, "sample/HelloWorld",
                null, "java/lang/Object", null);

        {
            MethodVisitor mv1 = cw.visitMethod(ACC_PUBLIC, "<init>", "()V", null, null);
            mv1.visitCode();
            mv1.visitVarInsn(ALOAD, 0);
            mv1.visitMethodInsn(INVOKESPECIAL, "java/lang/Object", "<init>", "()V", false);
            mv1.visitInsn(RETURN);
            mv1.visitMaxs(0, 0);
            mv1.visitEnd();
        }

        {
            MethodVisitor mv2 = cw.visitMethod(ACC_PUBLIC, "test", "()V", null, null);
            Label startLabel = new Label();
            Label endLabel = new Label();
            Label exceptionHandlerLabel = new Label();
            Label returnLabel = new Label();

            // 第1段
            mv2.visitCode();
            // visitTryCatchBlock可以在这里访问
            mv2.visitTryCatchBlock(startLabel, endLabel, exceptionHandlerLabel, "java/lang/InterruptedException");

            // 第2段
            mv2.visitLabel(startLabel);
            mv2.visitFieldInsn(GETSTATIC, "java/lang/System", "out", "Ljava/io/PrintStream;");
            mv2.visitLdcInsn("Before Sleep");
            mv2.visitMethodInsn(INVOKEVIRTUAL, "java/io/PrintStream", "println", "(Ljava/lang/String;)V", false);
            mv2.visitLdcInsn(new Long(1000L));
            mv2.visitMethodInsn(INVOKESTATIC, "java/lang/Thread", "sleep", "(J)V", false);
            mv2.visitFieldInsn(GETSTATIC, "java/lang/System", "out", "Ljava/io/PrintStream;");
            mv2.visitLdcInsn("After Sleep");
            mv2.visitMethodInsn(INVOKEVIRTUAL, "java/io/PrintStream", "println", "(Ljava/lang/String;)V", false);

            // 第3段
            mv2.visitLabel(endLabel);
            mv2.visitJumpInsn(GOTO, returnLabel);

            // 第4段
            mv2.visitLabel(exceptionHandlerLabel);
            mv2.visitVarInsn(ASTORE, 1);
            mv2.visitVarInsn(ALOAD, 1);
            mv2.visitMethodInsn(INVOKEVIRTUAL, "java/lang/InterruptedException", "printStackTrace", "()V", false);

            // 第5段
            mv2.visitLabel(returnLabel);
            mv2.visitInsn(RETURN);

            // 第6段
            // visitTryCatchBlock也可以在这里访问
            // mv2.visitTryCatchBlock(startLabel, endLabel, exceptionHandlerLabel, "java/lang/InterruptedException");
            mv2.visitMaxs(0, 0);
            mv2.visitEnd();
        }

        cw.visitEnd();

        // (3) 调用toByteArray()方法
        return cw.toByteArray();
    }
}
```

### 4.3. 验证结果

```java
import java.lang.reflect.Method;

public class HelloWorldRun {
    public static void main(String[] args) throws Exception {
        Class<?> clazz = Class.forName("sample.HelloWorld");
        Object obj = clazz.newInstance();

        Method method = clazz.getDeclaredMethod("test");
        method.invoke(obj);
    }
}
```

### 4.4. 小总结

通过上面的示例，我们注意三个知识点：

- 第一点，如何使用`ClassWriter`类。
- 第二点，在使用`MethodVisitor`类时，其中`visitXxx()`方法需要遵循的调用顺序。
- 第三点，如何通过`Label`类来实现try-catch语句。

有一个问题，`visitTryCatchBlock()`方法为什么可以在后边的位置调用呢？这与`Code`属性的结构有关系：

```
Code_attribute {
    u2 attribute_name_index;
    u4 attribute_length;
    u2 max_stack;
    u2 max_locals;
    u4 code_length;
    u1 code[code_length];
    u2 exception_table_length;
    {   u2 start_pc;
        u2 end_pc;
        u2 handler_pc;
        u2 catch_type;
    } exception_table[exception_table_length];
    u2 attributes_count;
    attribute_info attributes[attributes_count];
}
```

因为instruction的内容（对应于`visitXxxInsn()`方法的调用）存储于`Code`结构当中的`code[]`内，而try-catch的内容（对应于`visitTryCatchBlock()`方法的调用），存储在`Code`结构当中的`exception_table[]`内，所以`visitTryCatchBlock()`方法的调用时机，可以早一点，也可以晚一点，只要整体上遵循`MethodVisitor`类对就于`visitXxx()`方法调用的顺序要求就可以了。

```
|                |          |     instruction     |
|                |  label1  |     instruction     |
|                |          |     instruction     |
|    try-catch   |  label2  |     instruction     |
|                |          |     instruction     |
|                |  label3  |     instruction     |
|                |          |     instruction     |
|                |  label4  |     instruction     |
|                |          |     instruction     |
```

## 5. 总结

本文主要对`Label`类的示例进行介绍，内容总结如下：

- 第一点，`Label`类的主要作用是实现程序代码的跳转，例如，if语句、switch语句、for语句和try-catch语句。
- 第二点，在生成try-catch语句时，`visitTryCatchBlock()`方法的调用时机，可以早一点，也可以晚一点，只要整体上遵循`MethodVisitor`类对就于`visitXxx()`方法调用的顺序就可以了。