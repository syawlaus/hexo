title: Unity 学习 - NavMesh
date: 2016-06-18 18:00
categories: Unity
---

在 Unity 官方教程 [Survival Shooter](https://unity3d.com/cn/learn/tutorials/projects/survival-shooter-tutorial) 中，玩家需要不停射击靠近的敌人，避免敌人的攻击。当玩家移动时，敌人也一直跟随玩家的位置移动，并且会绕开场景上的障碍物。见下图：

<!-- more -->

![](/images/unity/navmesh/survival shooter.png)

要实现这个功能，需要用到 Unity 的 [NavMesh](http://unity3d.com/cn/learn/tutorials/topics/navigation) 功能。

> In order to get our characters navigating around the scene, we first need to create a navmesh.

> 为了让我们的角色在场景里导航，我们首先需要创建一个 navmesh。

> Traditional navigation around 3D objects in a scene is slow and infeeicient due to the complexities of the models. That is why Unity employs something called a Navmesh. A navmesh is a very simple 3D mesh which is derived from the geometry of more complicated elements in a scene. This more basic navmesh is much easire to navigate and path-find within.

> 由于模型的复杂性，传统的在场景里围绕三维物体导航很缓慢并低效。所以 Unity 引入了 NavMesh。NavMesh 是一个很简单的三维 mesh，来源于场景里更复杂的元素几何体。这个基础的 NavMesh 使得在场景里导航和寻路更简单。

要实现这个需求分为三步：

* 对整个场景使用 NavMesh Baking 定义敌人寻路的范围
* 为敌人设置 NavMesh Agent
* 在脚本中把敌人的移动目标设为玩家当前位置

---

# NavMesh Baking

创建 NavMesh 的过程，称为 Baking。

首先我们要对整个场景使用 NavMesh Baking，来定义敌人寻路的范围。我们先打开 Navigation view：

![](/images/unity/navmesh/navigation.png)

Navigation view 显示如下：

![](/images/unity/navmesh/navigation-view-object-tab.png)

在 Baking 前，我们必须告诉 Unity 哪些对象是 Navigation Static。Navigation Static 的对象是静态的不会移动，用于计算场景中 Agent 允许移动的范围。

## Object 过滤

我们在 Navigation view 中的 Object tab 来检查哪些对象是 Navigation Static。

“All” 表示我们想要检查 Hierarchy 中所有 GameObject 的 Inspector 是否勾选了 Navigation Static，见下图：

![](/images/unity/navmesh/inspector-navigation-static.png)

“Mesh Renderers” 表示我们只想检查 Hierarchy 中所有带有 Mesh Renderer 组件的 GameObject 的 Inspector 是否勾选了 Navigation Static。我们可以把某些不想 Baking 的 GameObject 去掉，只要取消勾选 Navigation Static 即可。见下图：

![](/images/unity/navmesh/navigation-view-object-tab-mesh-renderers.png)

“Generate OffMeshLinks” 表示是否生成 Off-mesh links。在下文会有讲述，暂不展开。

“Terrains” 表示我们想要检查 Hierarchy 中所有带有 Terrain 组件的 GameObject 的 Inspector 是否勾选了 Navigation StaticTerrains”。

## Bake 设置

Bake tab 如下图：

![](/images/unity/navmesh/navigation-view-bake-tab.png)

各个选项分别表示：

* Baked Agent Size
    * Agent Radius - Agent 到墙壁或其它障碍物的距离。
    * Agent Height - Agent 高度。
    * Max Slope - Agent 能移动上去的最大倾斜角。
    * Step Height - Agent 能移动的高度（如台阶），高于本高度被认为是障碍物，低于本高度则 Agent 能移动到其上方。
* Generated Off Mesh Links
    * Drop Height - 如果这个值为正数，Off-mesh links 会放置在高度差小于这个值的相邻的 NavMesh 表面上。比如 Drop Height 为 10，场景上有两个平台，高度差小于 10，那么在 Baking 时就会自动生成两个平台之间的 Off-Mesh Links。见下图 1。
    * Jump Distance - 如果这个值为正数，off-mesh links 会放置在水平距离差小于这个值的相邻的 NavMesh 表面上。比如 Jump Distance 为 10，场景上有两个平台，水平距离差小于 10，那么在 Baking 时就会自动生成两个平台之间的 Off-Mesh Links。见下图 2。
* Advanced
    * Manual Voxel Size - 手动设置体素大小。体素即“体积像素”（VOlume piXEL），概念上类似二维空间的最小单位 - 像素，体素是三维空间分割上的最小单位。见下图 3。
    * Voxel Size - 需要设计的体素大小。
    * Min Region Area - NavMesh 里的子区域的最小大小。见下图 4。
    * Height Mesh - 勾选表示优化 Agent 移动的精度，会使 Baking 速度变慢。见下图 5。

![](/images/unity/navmesh/navigation-view-bake-tab-drop-height.svg)

<center>图 1：Drop Height（[图片来源](http://docs.unity3d.com/Manual/class-OffMeshLink.html)）</center>

![](/images/unity/navmesh/navigation-view-bake-tab-jump-distance.jpg)

<center>图 2：Jump Distance（[图片来源](http://www.ceeger.com/Components/class-OffMeshLink.html)）</center>

![](/images/unity/navmesh/navigation-view-bake-tab-voxel.jpg)

<center>图 3：Voxel（[图片来源](http://www.bilderzucht.de/blog/3d-pixel-voxel/)）</center>

![](/images/unity/navmesh/navigation-view-bake-tab-min-region-area.png)

<center>图 4：Min Region Area（[图片来源](http://docs.unity3d.com/Manual/nav-AdvancedSettings.html)）</center>

![](/images/unity/navmesh/navigation-view-bake-tab-height-mesh.svg)

<center>图 5：Height Mesh（[图片来源](http://docs.unity3d.com/Manual/nav-HeightMesh.html)）</center>

## Areas 设置

Areas tab 如下图：

![](/images/unity/navmesh/navigation-view-areas-tab.png)

NavMesh 生成的可行走区域（walkable area）都默认分配了一个 Layer 和对应的 cost。Layer 的默认 cost 为 1。cost 越高，表示 NavMesh Agent 在寻路时该 Layer 被选择的优先级越低。比如，场景中有一条河，Agent 既可以游泳过河，也可以走桥过河。如果游泳的 cost 比过桥高，那么 Agent 会优先选择走桥过河。

**注意**：我们修改了 Areas tab 的设置时，要 re-bake 一次。

## Bake

在完成三个 Object, Bake, Areas 三个 tab 里的设置后，我们按下 Bake 按钮，Unity 就会开始为我们生成 NavMesh 了。可行走区域会标记为蓝色，见下图：

![](/images/unity/navmesh/baking-done.png)

---

# NavMesh Agent

## NavMesh Agent 设置

在完成场景 NavMesh Baking 后，为需要寻路的 GameObject 添加 NavMesh Agent 组件。NavMesh Agent 负责为其绑定的 GameObject 里导航到目标位置，并在 NavMesh 里寻路时绕开障碍物。在 Survival Shooter 中，我们为 Zombunny 添加 NavMesh Agent 组件（Add component -> Navigation -> Nav Mesh Agent），如下图：

![](/images/unity/navmesh/navmesh-agent.png)

各个选项分别表示：

* Agent Size
    * Radius - Agent 半径，用于计算与障碍物或其它 Agent 的碰撞。
    * Height - Agent 高度。
    * Base Offset - Offset of the collision cylinder in relation to the transform pivot point，见下图 1。
* Steering
    * Speed - 最大移动速度。
    * Angular Speed - 最大旋转角速度。
    * Acceleration - 最大加速度。
    * Stopping Distnace - 当 Stopping Distance 接近目标位置时，Agent 会停止移动。
    * Auto Braking - 启用时，Agent 在到达目标位置时会减缓移动。对于在多个地点来回巡逻的行为，你应该禁用 Auto Braking。
* Obstacle Avoidance
    * Quality - 如果你有很多 Agent，那么应该降低 Quality 来节省 CPU 计算时间。Quality 设为 None，Unity 只处理碰撞检测，但不会主动绕开障碍物或其它 Agent。
    * Priority - 本 Agent 在绕开时会自动忽略其它低优先级的 Agent。Priority 应在 0－99 之间，越小表示优先级越高。
* Path Finding
    * Audo Traverse Off Mesh Link - 启用时，Agent 自动在 OffMeshLinks 移动。如果你想使用 animation 或其它具体的在 OffMeshLinks 移动的方式，应禁用。
    * Audo Repath - 启用时，Agent 在到达子路径时会尝试再次寻路。如果没有通往目标位置的路径，会生成一条离目标位置最近的 Agent 可到达的子路径。
    * Area Mask - 描述了 Agent 在寻路时会考虑使用哪个 area type。当你为 NavMesh Baking 准备 mesh 时，你可以为不同的 mesh 设置不同的 area type。例如，你可以标记楼梯为特殊的 area type，并禁止某些角色使用楼梯。

![](/images/unity/navmesh/navmesh-agent-base-offset.svg)

<center>图 1：Base Offset（[图片来源](http://docs.unity3d.com/Manual/class-NavMeshAgent.html)，详细解释见[ Details 部分](http://docs.unity3d.com/Manual/class-NavMeshAgent.html)）</center>

## 脚本设定寻路目标

需要寻路的 GameObject 是 Zombunny，那么我们打开 Zombunny 绑定的脚本 Enemy_Movement.cs，代码如下（已省略无关代码）：

```csharp
using UnityEngine;
using System.Collections;

public class EnemyMovement : MonoBehaviour {
    private Transform playerTransform;
    private NavMeshAgent agent;

    void Awake () {
        // find player in code
        playerTransform = GameObject.FindGameObjectWithTag ("Player").transform;

        // get navmesh agent reference
        agent = GetComponent <NavMeshAgent> ();
    }

    void Update () {
        agent.SetDestination (playerTransform.position);
    }
}
```

代码很简单，只要获取 Zombunny 的 NavMesh Agent 引用，再 SetDestination 为 Player.transform.position 即可。

---

# 参考资料

* [Unity 3D AI: NavMesh Navigation](https://www.binpress.com/tutorial/unity3d-ai-navmesh-navigation/119)
