title: Unity 学习 - Coroutine
date: 2016-05-28 19:00
categories: Unity
---

#示例

最近在学习 Unity 的 [Space Shooter](https://unity3d.com/cn/learn/tutorials/projects/space-shooter-tutorial) 教程，里面有一个游戏逻辑是，游戏开始后，每秒生成一块陨石在随机位置掉落。实现的方式是（省略了不相干 C# 的代码）：

<!-- more -->

```csharp
void Start() {
    StartCoroutine(spawnRock());
}

IEnumerator spawnRock() {
    while (true) {
        Instantiate(rock, randomPosition, randomRotation);
        yield return new WaitForSeconds(1);
    }
}
```

从上面代码中看到，`spawnRock` 是一个 coroutine（协程），调用方式为 `StartCoroutine(spawnRock())`，我的疑问是：

* 什么是 coroutine？
* 什么是 IEnumerator？
* 为什么 coroutine 的返回值是一个 IEnumerator？
* `yield return new WaitForSeconds(1);` 这行代码是什么意思？
* yield 这个关键字有什么作用？
* 返回的 `new WaitForSeconds(1)` 为什么是一个 IEnumerator？

这一连串的疑问，让我对 coroutine 越发好奇，接下来就逐一解开谜题吧。

我的思路是，首先需要弄清楚 coroutine 的概念，什么是 coroutine，上面代码的语法如何实现 coroutine。

---

#什么是 coroutine？

首先看看 coroutine 的[维基百科词条](https://en.wikipedia.org/wiki/Coroutine)：

> Coroutines are computer program components that generalize subroutines for nonpreemptive multitasking, by allowing multiple entry points for suspending and resuming execution at certain locations. Coroutines are well-suited for implementing more familiar program components such as cooperative tasks, exceptions, event loop, iterators, infinite lists and pipes.

> coroutine 是计算机程序组件，用于对非抢占式多任务一般化子例程。一般化的方式是，允许多个程序入口在特定位置挂起和恢复执行。Coroutine 合适用于实现常用程序组件，如协作式任务、中断、事件循环、迭代、无限列表和管道。

嗯，解释得有点晦涩，还是不太明白。再看看其它解释，[What is a coroutine?](http://stackoverflow.com/questions/553704/what-is-a-coroutine)：

> Coroutines are a general control structure whereby flow control is cooperatively passed between two different routines without returning.

> coroutine 是一个一般性的控制结构，其中的控制流是在两个不同的例程中合作传递，而无需返回。

> The main difference between threads and coroutines is that, conceptually (or literally, in a multiprocessor machine), a program with threads runs several threads in parallel. Coroutines, on the other hand, are collaborative: at any given time, a program with coroutines is running only one of its coroutines, and this running coroutine suspends its execution only when it explicitly requests to be suspended.

>thread 和 coroutine 的主要区别是，从概念上讲，（在一个多处理器机器中）一个多线程的程序能并行运行多个线程，而 coroutine 则是协作式的，在任何时刻，一个多协程的程序只能运行其中一个 coroutine，当明确需要挂起 coroutine 时才会被挂起。

开始有点明白了，概念很像以前的[分时系统](https://zh.wikipedia.org/zh/%E5%88%86%E6%99%82%E7%B3%BB%E7%B5%B1)，多个用户使用同一台计算机，操作系统把 CPU 的计算资源按时间片的方式分配给各个用户（如 10 个用户同时使用，分时系统把每秒里的 0.1 秒分配给每位用户）。由于时间片很短，某个用户便感觉不到其他用户也在使用该计算机。如果某用户的任务在一个时间片里无法完成，就挂起任务，直到下次轮转到他的时间片时再继续执行。

看来可以把 coroutine 理解为 **Collaborative Routine（协作例程）**。

回看上面的分时系统的例子，我把用户数缩减为两个，粗略地画出下面的图：

    各用户  |__A__|__B__|__A__|__B__|...
    时间片  0    0.1   0.2   0.3   0.4...

可以看到，每两个时间片组成一次轮转：

* 用户 A 使用 [0, 0.1] 时间片
* 用户 B 使用 [0.1, 0.2] 时间片
* 用户 A 使用 [0.2, 0.3] 时间片
* 用户 B 使用 [0.3, 0.4] 时间片
* ...

回到 coroutine 的理解中，我对 coroutine 的理解是，**coroutine 是一个能暂停执行的函数，下次调用 coroutine 时，从上次暂停的地方，以上次的状态继续执行**。那么，两个 coroutine 之间是如何协作的？coroutineA 如何暂停执行，并开始执行 coroutineB 的呢？

---

#yield 和 IEnumerator

yield 这个 C# 关键字的作用，参见 [MSDN](https://msdn.microsoft.com/en-us/library/9k7k7cf0.aspx) 说明：

> When you use the yield keyword in a statement, you indicate that the method, operator, or get accessor in which it appears is an iterator. Using yield to define an iterator removes the need for an explicit extra class (the class that holds the state for an enumeration, see `IEnumerator<T>` for an example) when you implement the IEnumerable and IEnumerator pattern for a custom collection type.

> 当你在一个语句中使用 yield 关键字时，表明你想要语句中的方法、操作符、get 存取器出现在一个迭代器中。使用 yield 定义一个迭代器，当你对自定义的集合类型实现 IEnumerable 和 IEnumerator 模式时，无需定义一个明确的额外的类（该类存储了一个枚举的信息，例子请见 `IEnumerator<T>`）。

即 **yield 用于定义一个迭代器，把 yield 描述的语句放到迭代器对应的集合类中**。

MSDN 说明中提到了一个“迭代器”。这跟 IEnumerator 有什么关系吗？

我们回看本文开头的 Space Shooter 代码：

```csharp
IEnumerator spawnRock() {
    while (true) {
        Instantiate(rock, randomPosition, randomRotation);
        yield return new WaitForSeconds(1);
    }
}
```

根据 MSDN 说明，`yield return new WaitForSeconds(1);` 即是把 `new WaitForSeconds(1)` 放在一个 yield 定义的迭代器对应的集合类中，难道这个迭代器就是 IEnumerator？

我们看看看 IEnumerator 的[定义][1]：

> Supports a simple iteration over a non-generic collection.

> 支持对非通用集合的一个简单迭代。

即 **IEnumerator 是一个迭代器，能对我们自定义的集合进行迭代操作**。

下图是 IEnumerator 的字段和方法列表：

![](/images/unity/coroutine/properties_and_methods_of_ienumerator_in_csharp.png)

那么，`yield return new WaitForSeconds(1);` 即是把 `new WaitForSeconds(1)` 放在一个 yield 定义的迭代器对应的集合类中，这个推断应该没错。现在的疑问是：

* 为什么 coroutine 的返回值是一个 IEnumerator？
* 由 `yield return new WaitForSeconds(1);` 所生成的 IEnumerator，会用在什么地方？

继续查看 yield 的 [MSDN 定义](https://msdn.microsoft.com/en-us/library/9k7k7cf0.aspx) 的 Remarks 部分：

> You consume an iterator method by using a foreach statement or LINQ query. Each iteration of the foreach loop calls the iterator method. When a yield return statement is reached in the iterator method, expression is returned, and the current location in code is retained. Execution is restarted from that location the next time that the iterator function is called.

> 你在使用 foreach 语句或 LINQ 查询时会调用一个迭代器方法。foreach 循环里的每次迭代都调用迭代器方法。当在迭代器方法里执行到 yield return 语句时，（`yield return <expression>;` 里的）expression 会被返回，并且代码当前位置将保存。下次迭代器方法被调用时，从该位置开始执行。

编写下面的代码来验证一下：

```csharp
using UnityEngine;
using System.Collections;

public class CoroutineTest : MonoBehaviour {

    void Start () {
        foreach (int i in getIntegers()) {
            Debug.Log("i = " + i);
        }
    }
    
    IEnumerable getIntegers() {
        Debug.Log("yield return 1");
        yield return 1;
    
        Debug.Log("yield return 2");
        yield return 2;
    
        Debug.Log("yield return 3");
        yield return 3;
    
        Debug.Log("yield return 4");
        yield return 4;
    
        Debug.Log("yield return 5");
        yield return 5;
    }
}


// 输出结果：
// yield return 1
// i = 1
// yield return 2
// i = 2
// yield return 3
// i = 3
// yield return 4
// i = 4
// yield return 5
// i = 5
```

我们再看看下面的代码：

```csharp
IEnumerator foo() {
    print("Hello");
    yield return new WaitForSeconds(1);
    print("world");
}
```

在第一次调用 foo 时，输出 Hello 后，把 `new WaitForSeconds(1)` 放到 IEnumerator 对应的集合类中。下次调用 foo 时，从 `print("world");` 一行开始执行。那么，什么时候才“下次调用 foo”？

我们再来看一个例子：

```csharp
void Start () {
    Debug.Log("开始执行 Start");
    Debug.Log("start1");
    StartCoroutine(Test());
    Debug.Log("start2");
    Debug.Log("完成执行 Start");
}

IEnumerator Test() {
    Debug.Log("test1");
    yield return null;
    Debug.Log("test2");
}


// 输出结果：
// 开始执行 Start
// start1
// test1
// start2
// 完成执行 Start
// test2
```

为什么输出“完成执行 Start”后，能调用 `Test` 继续执行？

我们看回这句话：

> Execution is restarted from that location the next time that the iterator function is called.

什么是“迭代器函数”（iterator function）？是 `IEnumerator.MoveNext()` 吗？

在 [Coroutine and Iterator Function Tricks](http://jacksondunstan.com/articles/3036/comment-page-1) 这篇文章里说到：

> An *iterator function* is a function that uses the `yield return` or `yield break` statements. 

那么迭代器函数应该就是 coroutine 本身。

在 [What is yield return in C#?](http://stackoverflow.com/questions/1407162/what-is-yield-return-in-c) 里，Chris Chilvers 的回答是：

> yield return 1; yield return 2; there that builds a simple finite state machine, each call to MoveNext on the enumerator resumes from the previous state then runs until the next yield, which is the next state.

> yield return 1; yield return 2; 构建了一个简单的有限状态机，每次调用该枚举器的 MoveNext，都会从上一个状态恢复，一直执行直到下一个 yield，即是下一个状态。

那么什么时候会调用 `MoveNext`？现在能确定的是，下一次调用 coroutine 时会调用 `MoveNext`。

To be continued.










[1]: https://msdn.microsoft.com/en-us/library/system.collections.ienumerator(v=vs.110).aspx
