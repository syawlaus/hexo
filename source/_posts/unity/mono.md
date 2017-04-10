title: Unity 学习 - Mono
date: 2016-08-10 00:20
categories: Unity
---

我们都知道，在 Unity 开发中，一般使用 MonoDevelop 来编写 C# 代码，很多的类继承自 MonoBehaviour。而 Unity 强大的跨平台能力，也是得益于 Mono。那么 Mono 到底是什么？

<!-- more -->

---

#Mono

我们先看看 [Mono 的维基百科条目](https://zh.wikipedia.org/wiki/Mono)：

> Mono是一个由 Xamarin 公司（先前是 Novell，最早为 Ximian）所主持的自由开放源代码项目。该项目的目标是创建一系列匹配 ECMA 标准（Ecma-334 和 Ecma-335）的 .NET 工具，包括 C# 编译器和通用语言架构。与微软的 .NET Framework（共通语言运行平台）不同，Mono 项目不仅可以运行于 Windows 系统上，还可以运行于 Linux，FreeBSD，Unix，OS X 和 Solaris，甚至一些游戏平台，例如：Playstation 3，Wii 或 XBox 360。

##.NET Framework

什么是 [.NET Framework](https://zh.wikipedia.org/wiki/.NET%E6%A1%86%E6%9E%B6)？

> .NET 框架（.NET Framework）是由微软开发，一个致力于敏捷软件开发（Agile software development）、快速应用开发（Rapid application development）、平台无关性和网络透明化的软件开发平台。.NET 是微软为 2000 年代对服务器和桌面型软件工程迈出的第一步。.NET 包含许多有助于互联网和内部网应用迅捷开发的技术。

> .NET 框架是微软公司继 Windows DNA 之后的新开发平台。.NET 框架是以一种采用系统虚拟机运行的编程平台，以通用语言运行库（Common Language Runtime）为基础，支持多种语言（C#、F#、VB.NET、C++、Python 等）的开发。

> .NET 也为应用程序接口（API）提供了新功能和开发工具。这些革新使得程序设计员可以同时进行 Windows 应用软件和网络应用软件以及组件和服务（web 服务）的开发。.NET 提供了一个新的反射性的且面向对象程序设计编程接口。.NET 设计得足够通用化从而使许多不同高级语言都得以被汇集。

> 甲骨文公司的 Java 编程语言和 Java 平台企业版技术是 .NET 平台的竞争对手之一。

##CLI

什么是[通用语言架构](https://zh.wikipedia.org/wiki/%E9%80%9A%E7%94%A8%E8%AF%AD%E8%A8%80%E6%9E%B6%E6%9E%84)？

> 通用语言基础架构（Common Language Infrastructure，简称 CLI）是一个开放的技术规范。它是由微软联合惠普以及英特尔于 2000 年向 ECMA 倡议的。通用语言基础架构定义了构成 .NET Framework 基础结构的可执行码以及代码的运行时环境的规范，它定义了一个语言无关的跨体系结构的运行环境，这使得开发者可以用规范内定义的各种高级语言来开发软件，并且无需修正即可将软件运行在不同的计算机体系结构上。国际标准 CLI 定义在 ISO/IEC 23271，最新版本在 2012 年修定。

> 通用语言运行库（CLR）则是微软对此标准（CLI）的实现。

![](/images/unity/mono/Overview_of_the_Common_Language_Infrastructure.png)

<center>（[图片来源](https://zh.wikipedia.org/wiki/File:Overview_of_the_Common_Language_Infrastructure.svg)）</center>

我的理解是，Mono 是 CLI 的一个实现，扮演着中间件的角色。上层支持 CLI，下层兼容多个不同平台。程序员只要编写一份 CLI 支持的语言的代码，就能在 Mono 支持的平台上运行。

![](/images/unity/mono/Mono_architecture.png)

<center>（[图片来源](https://zh.wikipedia.org/wiki/File:Mono_architecture.svg)）</center>

> 在本地的 .NET / Mono 应用程序中，所有代码都是托管的，也就是说，是受管于 CLI 式的内存管理和线程安全管理的。

##Mono Runtime

简单理解，Mono Runtime 就是为其托管的代码提供运行时支持（如代码由 Runtime 执行而不是由 CPU 执行，对象 GC 等等）的环境。

##回看 Mono

Mono 到底是什么？

.NET 只能运行在 Windows 上，而 Mono 则是对 CLI 的另一种实现，可以运行在多个平台上（Windows、Mac OS、Linux 等等）。Mono 有一个 C# 编译器，一个运行时环境（Runtime Environment），还有很多库类。这跟 Java 平台很类似。这样能实现操作系统无关，甚至是 CPU 架构无关，从而达到跨平台的效果。详见[What is Mono? Is it a compiler? A language? Or what?](http://answers.unity3d.com/questions/8555/what-is-mono-is-it-a-compiler-a-language-or-what.html)。

需要注意的是，Unity 不是基于 Mono 构建的，而是“系统原生”应用程序。Unity 使用 Mono 作为其脚本框架。
