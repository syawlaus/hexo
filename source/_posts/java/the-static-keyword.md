title: Java static 关键字
date: 2015-05-07 19:00
categories: Java
---

Java static 关键字可以用来描述字段、方法。

<!-- more -->

---

# static field

静态字段，属于类所有，不是属于具体的实例所有，所以全部实例**共用**一个静态字段。[Oracle Java Documentation](https://docs.oracle.com/javase/tutorial/java/javaOO/classvars.html) 把静态字段也称为**类变量（Class Variable）**，而把一般字段称为**实例变量（Instance Variable）**。

有两种方式可以访问静态字段：

1. 类名调用，无需创建实例：`ClassName.staticField`
2. 实例调用：`instanceName.staticField`

推荐使用类名调用，因为更能清楚表明该字段的 static 属性。另外，[Thinking in Java, 4th edition](http://mindview.net/Books/TIJ4)，the **static** keyword 章节提到，在某些情况下编译器能对此进行优化（"in some cases it gives the compiler better opportunities for optimization"）。

这个问题 [What does the 'static' keyword do in a class?](http://stackoverflow.com/questions/413898/what-does-the-static-keyword-do-in-a-class) 的其中一个回答提到：

> This discussion has so far ignored classloader considerations. Strictly speaking, Java static fields are shared between all instances of a class for a given classloader.

以后再深入研究一下静态字段与 ClassLoader 的关系。

接下来用代码试验：

1. 静态字段/方法可直接由类名调用
2. 全部实例共用一个静态字段
3. 静态字段保存在一个固定的内存位置  // TODO
4. 一般字段保存在多个不同的内存位置  // TODO

```java
public class Main {
    public static void main(String[] args) {
        // 试验1：静态字段/方法可直接由类名调用
        System.out.println("StaticTest.i = " + StaticTest.i);
        StaticTest.f();
        System.out.println("");
        
        // 试验2：所有实例共用一个静态字段
        StaticTest st1 = new StaticTest();
        StaticTest st2 = new StaticTest();
        st1.i++;
        System.out.println("st1.i = " + st1.i);
        System.out.println("st2.i = " + st2.i);
        System.out.println();
        
        // TODO 试验3：静态字段保存在一个固定的内存位置
        // TODO 试验4：一般字段保存在多个不同的内存位置
    }
}

class StaticTest {
    static int i = 0;
    static void f() {
        System.out.println("This is StaticTest.f()");
    }
}


//*****输出结果*****
StaticTest.i = 0
This is StaticTest.f()

st1.i = 1
st2.i = 1
```

试验1，从程序第4～5行看到，类名调用 `StaticTest.i` 和 `StaticTest.f()`，编译器不报错，说明类名调用是可用的。

试验2，从程序第8～13行及输出结果看到，静态字段也可以通过实例调用，且多个实例共用一个静态字段（否则 `st2.i` 的值应该为0）。

---

# static method

静态方法，与静态字段一样，由类所有，也称为**类方法（Class Method）**，有两种调用方式：

有两种方式可以调用静态方法：

1. 类名调用，无需创建实例：`ClassName.staticMethod(args)`
2. 实例调用：`instanceName.staticMethod(args)`

推荐使用类名调用。

**注意**：一般字段/方法，只能通过实例调用。
