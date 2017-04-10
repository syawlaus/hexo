title: 《3D 游戏编程大师技巧》第 1 章学习笔记
date: 2016-06-26 16:30
categories: 计算机图形学
---

最近在学习《3D 游戏编程大师技巧》第 1 章，摘抄一些我认为重要的信息，并添加上自己的一些思考。

<!--- more -->

> 本书将从一种通用的角度来介绍图形系统。书中的代码将以《Windows 游戏编程大师技巧》中创建的引擎为基础，该引擎是以标准的线性寻址方式建立一个双缓存图形系统。

> 本书不是讨论 3D 引擎，而是介绍如何编写 3D 引擎。

> 您必须学习一种有利于实时应用程序和仿真的全新编程方式，而不是您习惯的单线、事件驱动或顺序逻辑程序。一个视频游戏本质上是一个持续不断的循环，它执行逻辑并在屏幕上绘制图像——通常以 30～60帧/秒或更高的速度进行绘制。

![](/images/computer-graphics/tricks-of-the-3d-game-programming-gurus/chapter1-notes/figure-1.1.png)

> 下一帧图像通常在离屏（offscreen）缓存中绘制，因此用户无法看到渲染过程。下一帧图像被快速复制到可视区域，产生动画影像。在基于 3D 软件的引擎中，将由一个非常复杂的 3D 图形流水线来渲染构成世界的成千上万（甚至数百万）个多边形；在基于 OpenGL 或 Direct3D 的硬件加速引擎中，大部分工作都由硬件来承担。

> 在大多数情况下，游戏循环是一个包含多种状态的 FSM（Finite State Machine，有限状态机）。

![](/images/computer-graphics/tricks-of-the-3d-game-programming-gurus/chapter1-notes/figure-1.2.png)

> 您应该意识到，犯一个错误并重新编写 500 行代码，要比彩一个不好的编码结构强得多。因此，如果您在编程过程中发现了问题，应重新进行评价，并确认您所节省的时间是值得的。
>
> 视频游戏编程中 90% 的工作是数据操作。保持数据尽可能简单并可见，以便可以迅速访问它并执行所需的操作。确保数据结构与需要解决的问题相称。

![](/images/computer-graphics/tricks-of-the-3d-game-programming-gurus/chapter1-notes/figure-1.17.png)

> 3D 图像是如何生成的呢？（在 Raiders 里）敌方飞船是多边形物体，它定义了 3D 物体的外形。也就是说，它们是 2D 轮廓，而不是完全的 3D 物体。3D 的关键是透视投影。图 1.19 说明了正交投影与透视投影的差别，它们是 3D 图形系统中的两种基本投影类型。

![](/images/computer-graphics/tricks-of-the-3d-game-programming-gurus/chapter1-notes/figure-1.19.png)

![](/images/computer-graphics/tricks-of-the-3d-game-programming-gurus/chapter1-notes/figure-1.20.png)

对于图 1.20，**我的疑问：怎么知道透视投影的角度是多少？与摄像机到坐标系原点的距离有关吗？**

> 可以像移动 2D 物体那样移动 3D 多边形物体，在渲染之前使用透视投影方程对点进行变换，让物体能够在 3D 究竟正确地显示和移动。当然，将坐标 (x<sub>per</sub>, y<sub>per</sub>) 转换为实际屏幕坐标将涉及许多细节，但就现在而言，这些并不重要。您只需要知道 3D 物体以 3D 方式移动，并使用数学方法（考虑了透视）将 3D 物体投影到 2D 观察面（屏幕）上。
