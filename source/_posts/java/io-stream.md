title: Java I/O 流
date: 2016-01-12 19:00
categories: Java
---

我们在 Java 编程中经常会用到 I/O 流，如读取文件内容时使用 FileInputStream，向文件写入内容时使用 FileOuputStream。但一直对流理解并不透彻，想通过本文进一步巩固这方面的知识。

<!-- more -->

---

# 什么是流？

流（Stream）是指一个数据序列，I/O 流即是输入/输出流。

以程序为基准，从数据端读入数据到程序，则是输入流，如下图：

![输入流](/images/java/io-stream/InputStream.png)

从程序输出数据到数据端，则是输出流，如下图：

![输出流](/images/java/io-stream/OutputStream.png)

数据端可以为多种形式，如磁盘文件、外部设备、网络 socket、其它程序，或内存数组（Memory Array）等。

流支持多种数据形式，如字节、基本数据类型、字符、对象等。

---

# 字节流（Byte Streams）

我们看看下面的 `CopyBytes` 程序（[程序及图片来源](https://docs.oracle.com/javase/tutorial/essential/io/bytestreams.html)）。

```java
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;


public class CopyBytes {
    public static void main(String[] args) throws IOException {
        FileInputStream in = null;
        FileOutputStream out = null;
        
        try {
            in = new FileInputStream("xanadu.txt");
            out = new FileOutputStream("outagain.txt");
            int c;
            while ((c = in.read()) != -1) {  // 注意：当没有输入时，read() 会一直阻塞
                out.write(c);
            }
        } finally {
            if (in != null) {
                in.close();
            }
            if (out != null) {
                out.close();
            }
        }
    }
}
```

CopyBytes 程序的循环过程如下图：

![CopyBytes](/images/java/io-stream/CopyBytes.png)

---

# 字符流（Character Streams）

除了处理字节输入/输出的 `Stream`，Java 还提供了处理字符输入/输出的 `Reader/Writer`。Java 默认使用 Unicode 编码来存储字符。字符流会自动把程序内部的格式转换为本地的字符集。

我们看看下面的 `CopyCharacters` 程序（[程序来源](https://docs.oracle.com/javase/tutorial/essential/io/charstreams.html)）。

```java
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;


public class CopyCharacters {
    public static void main(String[] args) throws IOException {
        FileReader inputStream = null;
        FileWriter outputStream = null;
        
        try {
            inputStream = new FileReader("xanadu.txt");
            outputStream = new FileWriter("characteroutput.txt");
            
            int c;
            while ((c = inputStream.read()) != -1) {
                outputStream.write(c);
            }
        } finally {
            if (inputStream != null) {
                inputStream.close();
            }
            if (outputStream != null) {
                outputStream.close();
            }
        }
    }
}
```

值得注意的是，`int c = FileReader.read()` 保存字符的后 16 位，而 `CopyBytes` 程序的 `int c = in.read()` 保存字符的后 8 位（为什么？）。

---

# 缓冲流（Buffered Streams）

上面的字节流和字符流都是没有缓冲的 I/O 流，即是说所有读写的数据都直接由系统处理。这样程序的效率不高，因为数据端经常是磁盘、网络等，程序需要等待数据的读写而不能处理数据。如果有一个缓冲区，从数据端读入一大段数据，再由程序处理，效率会高很多。

所谓的缓冲区（buffer），其实就是一块内存区域。但读到 [Buffered Streams - The Java Tutorials](https://docs.oracle.com/javase/tutorial/essential/io/buffers.html) 的以下内容，就有点不明白了：

>> To reduce this kind of overhead, the Java platform implements buffered I/O streams. Buffered input streams read data from a memory area known as a buffer; the native input API is called only when the buffer is empty. Similarly, buffered output streams write data to a buffer, and the native output API is called only when the buffer is full.

我的问题是：

1. 什么是“native input/output API”？
2. 为什么“the native input API is called only when the buffer is empty”？
3. 为什么“the native output API is called only when the buffer is full”？

在 StackOverflow 找到[这个回答](http://stackoverflow.com/questions/17873205/why-native-api-is-called-when-i-o-buffer-gets-empty/17873388#17873388)。

`native input/output API` 是由操作系统提供的执行 I/O 任务的 API。我的理解是，不管 buffer 是由 Java 缓冲流 API 申请得到，还是由 Java 缓冲流调用 Native API 申请得到的，buffer 对于我们写的程序来说都是黑盒。所以当 buffer 为空时，才把数据端数据读取到 buffer。当 buffer 为满时，才把 buffer 数据写入到数据端。那么 Buffered InputStream 读取数据时，如果 buffer 非空，就不调用 Native API，应该是直接获取到 buffer 的数据了。同理，Buffered OutputStream 写入数据时，先往 buffer 写入，直到 buffer 满了之后才调用 Native API 把 buffer 数据写入到数据端。那么应该要先等 Native API 执行完成并清空 buffer 后，Buffered OutputStream 才能继续往 buffer 写数据（暂不深究）。

把一个非缓冲流转换为缓冲流很容易：

    inputStream = new BufferedReader(new FileReader("filename.txt"));
    outputStream = new BufferedWriter(new FileWriter("filename.txt"));

`BufferedInputStream` 和 `BufferedOutputStream` 用来创建缓冲字节流，`BufferedReader` 和 `BufferedWriter` 用来创建缓冲字符流。

最后，我们可以通过调用 `BufferedOutputStream.flush()` 把 buffer 数据强制写入到数据端，而无需等待 buffer 满了之后才写入。

---

# 数据流（Data Streams)

数据流支持基本数据类型（`boolean`, `char`, `byte`, `short`, `int`, `long`, `float`, `and` `double`）及 `String` 值的二进制输入/输出。见下面的代码：

```java
import java.io.BufferedInputStream;
import java.io.BufferedOutputStream;
import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.nio.channels.FileLockInterruptionException;


public class Main {
    public static void main(String[] args) {
        String fileName = "out.txt";
        writeDataToFile(fileName);
        readDataFromFileAndPrint(fileName);
    }
    
    private static void writeDataToFile(String fileName) {
        try {
            DataOutputStream dos = new DataOutputStream(
                    new BufferedOutputStream(new FileOutputStream(fileName)));
            dos.writeBoolean(true);
            dos.writeChar('a');
            dos.writeByte('b');
            dos.writeInt(1024);
            dos.writeDouble(3.1415926);
            dos.writeUTF("I am syawlaus.");
            dos.flush();  // 因为使用了 BufferedOutputStream，上面数据未满 buffer，所以要 flush() 强制写入文件
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    
    private static void readDataFromFileAndPrint(String fileName) {
        try {
            DataInputStream dis = new DataInputStream(
                    new BufferedInputStream(new FileInputStream(fileName)));
           System.out.println(dis.readBoolean());  // read() 顺序需要跟 write() 相同
           System.out.println(dis.readChar());
           System.out.println(dis.readByte());
           System.out.println(dis.readInt());
           System.out.println(dis.readDouble());
           System.out.println(dis.readUTF());
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}


// 输出结果：
true
a
98
1024
3.1415926
I am syawlaus.
```

---

# 对象流（Object Streams）

对象流支持对象的输入/输出，对象流的类是 `ObjectInputStream` 和 `ObjectOutputStream`。

我们知道，对象里的成员变量通常是基本类型数据和其它类的对象。那么对象流的处理，是否对对象里的每个基本类型数据使用 DataInputStream／DataOutputStream 处理，其它类的对象继续使用 ObjectInputStream／ObjectOutputStream 递归处理呢？我们继续查阅 [Oracle 相关文档](http://docs.oracle.com/javase/tutorial/essential/io/objectstreams.html)。以下是[示例代码](http://docs.oracle.com/javase/tutorial/essential/io/examples/ObjectStreams.java)：

```java
import java.io.*;
import java.math.BigDecimal;
import java.util.Calendar;

public class ObjectStreams {
    static final String dataFile = "invoicedata";

    static final BigDecimal[] prices = { 
        new BigDecimal("19.99"), 
        new BigDecimal("9.99"),
        new BigDecimal("15.99"),
        new BigDecimal("3.99"),
        new BigDecimal("4.99") };
    static final int[] units = { 12, 8, 13, 29, 50 };
    static final String[] descs = {
        "Java T-shirt",
        "Java Mug",
        "Duke Juggling Dolls",
        "Java Pin",
        "Java Key Chain" };

    public static void main(String[] args) throws IOException, ClassNotFoundException {
        // write data
        ObjectOutputStream out = null;
        try {
            out = new ObjectOutputStream(
                    new BufferedOutputStream(new FileOutputStream(dataFile)));
            out.writeObject(Calendar.getInstance());
            for (int i = 0; i < prices.length; i ++) {
                out.writeObject(prices[i]);
                out.writeInt(units[i]);
                out.writeUTF(descs[i]);
            }
        } finally {
            out.close();
        }

        // read data
        ObjectInputStream in = null;
        try {
            in = new ObjectInputStream(
                    new BufferedInputStream(new FileInputStream(dataFile)));

            Calendar date = null;
            BigDecimal price;
            int unit;
            String desc;
            BigDecimal total = new BigDecimal(0);

            date = (Calendar) in.readObject();
            System.out.format ("On %tA, %<tB %<te, %<tY:%n", date);
            try {
                while (true) {
                    price = (BigDecimal) in.readObject();
                    unit = in.readInt();
                    desc = in.readUTF();
                    System.out.format("You ordered %d units of %s at $%.2f%n", unit, desc, price);
                    total = total.add(price.multiply(new BigDecimal(unit)));
                }
            } catch (EOFException e) {}
            System.out.format("For a TOTAL of: $%.2f%n", total);
        } finally {
            in.close();
        }
    }
}

// 输出结果：
On Wednesday, January 20, 2016:
You ordered 12 units of Java T-shirt at $19.99
You ordered 8 units of Java Mug at $9.99
You ordered 13 units of Duke Juggling Dolls at $15.99
You ordered 29 units of Java Pin at $3.99
You ordered 50 units of Java Key Chain at $4.99
For a TOTAL of: $892.88
```

调用 `writeObject(Object)` 时，该对象需要实现 `Serializable` 接口，否则会抛出 `java.io.NotSerializableException` 异常。上面的 `BigDecimal` 和 `Calendar` 都实现了 `Serializable` 接口。

当对象 A 里引用了其它对象 B、C，那么对象流 `writeObject()` 保存 A 的状态时，也需要保存 B、C 的状态。如果 B 里也引用了其它对象，则同理处理，见下图：

![对象流](/images/java/io-stream/ObjectStream.png)

---

# I/O 流的层次结构

InputStream 层次结构：

![输入流的层次结构](/images/java/io-stream/InputStream-hierarchy.png)

OutputStream 层次结构：

![输出流的层次结构](/images/java/io-stream/OutputStream-hierarchy.png)

---

# 参考资料

* [I/O Streams - The Java Tutorials](https://docs.oracle.com/javase/tutorial/essential/io/streams.html)
* [What exactly does “Stream” and “Buffer” mean in Java I/O?](http://stackoverflow.com/questions/15984789/what-exactly-does-stream-and-buffer-mean-in-java-i-o)
* [How java.io.Buffer* stream differs from normal streams?](http://stackoverflow.com/questions/1721387/how-java-io-buffer-stream-differs-from-normal-streams)
* [Java 对象序列化](http://syawlaus.github.io/blog/java/serialization/)
