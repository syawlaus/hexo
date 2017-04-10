title: Unity 学习 - 物理引擎
date: 2016-06-03 18:00
categories: Unity
---

在 Unity 项目里，会频繁使用到物理引擎，本文意在整理跟物理引擎相关的知识。

<!-- more -->

---

#Rigidbody

Rigidbody 是一个 Unity 组件，用于把 Unity 物理引擎作用到 GameObject 上。

---

#Collider

物理引擎通过 Rigidbody 进行碰撞检测计算时，需要知道各个 GameObject 的 volume。我们可以使用一个容器包裹 GameObject，并把该容器的 volume 作为 GameObject 的 volume 给予 Rigidbody 进行碰撞计算，这个容器称为 Collider（碰撞器）。

##static collider / static volume

静态非移动物体，如墙壁、地板，有 collider 但没有 Rigidbody 的 GameObject 都是 static collider，首次计算完其 position, rotation, scale 等信息就缓存起来，免得每一帧都重新计算。

##dynamic collider / dynamic volume

动态移动物体，如汽车、飞机，有 collider 且有 Rigidbody 的 GameObject 都是 dynamic collider，每一帧都要重新计算 position, rotation, scale 等信息。

给 static collider 加上 Rigidbody，就成了 dynamic collider。

##collider / trigger collider

两个 collider 接触，Unity 会生成物理碰撞效果。两个 collider 至少一个是 trigger collider，Unity 不生成物理碰撞效果。

影响碰撞计算的其中一个重要要素是，collider 是静态还是动态。当计算碰撞时，静态物体不会受碰撞所影响，而动态物体则会受到影响。物理引擎允许 collider volume 重叠，此时物理引擎仍会计算 collider volume 并追踪 collider 的重叠。但两个物体之间不会有实际的碰撞效果，这需要 collider 为 trigger collider 才行。当我们把 collider 设置为 trigger collider，可以通过“OnTrigger”事件消息来检测 trigger 之间的碰撞（即重叠）。

Unity 有多种 collider：

* Box / Sphere / Capsule Collider 是基础 collider，形状比较简单，可以简化碰撞检测时的计算量，优先使用。对于某些较复杂的模型，无法用 Box / Sphere / Capsule Collider 包裹的，或对碰撞检测精度比较高的，可使用 Mesh Collider。
* Mesh Collider，在对 Mesh Collider 进行碰撞检测时，Unity 必须对容器中与对方 collider 接触的每个三角形进行 position 检查。当我们使用 Mesh Collider 时，最好有一个简化的 mesh 用于简化计算量。Mesh Collider 持有 Mesh 的引用，默认跟 Inspector 里的 Mesh Filter 用到的 Mesh 一样，我们可以在 Mesh Collider -> Mesh 中选择一个简化的 Mesh 模型。使用 Mesh Collider 时，需要勾选 Convex，否则 Mesh Collider 不会被用于物理碰撞计算，在 Unity 编辑器 Scene View 里也不可视。

---

#Kinematic

Inspector 里有一项是 Kinematic，那么什么是 Kinematic？

运动学（Kinematics）是力学的一门分支，专门描述物体的运动，即物体在空间中的位置随时间而作出的改变，完全不考虑作用力或质量等影响运动的因素。

> Standard Rigidbody moves by forces, a kinematic Rigidbody will not react to physics forces and it can be animated and moved by its transform.

> 标准的 Rigidbody 受力而移动，一个 kinematic 的 Rigidbody 不会响应物理受力，而可以由其变换作出动作或移动。

> The "Is Kinematic" setting effects whether or not a rigidbody will react to physics. Ordinarily when a scene begins, all static geometry, meaning any non-rigidbody objects are checked once by the physics engine and not checked again for efficiency. However when you move a static object the physics engine must re-check all other static objects for the sake of accuracy, and this can be expensive to performance. To avoid this, Kinematic rigidbody objects can be used and moved via their transform by using the Translate function. This means that you can have physics objects that effect others but are not effected themselves.

> "Is Kinematic" 设置影响一个 Rigidbody 是否响应物理。通常一个场景启动，所有静态几何体，即没有 Rigidbody 的对象，都会被物理引擎检查一次，且为了效率，仅检查一次。然而当你移动了一个静态对象，物理引擎为了准确性，必须再次检查所有其它对象，这非常消耗计算性能。为了避免这个问题，Kinematic Rigidbody 可以通过它们的变换来使用或移动（调用 Translate 函数）。这表示你可以让（Kinematic Rigidbody）物理对象影响其它物理对象，但不影响自身。
