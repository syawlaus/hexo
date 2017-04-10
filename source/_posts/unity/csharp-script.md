title: Unity 学习 - C# 脚本
date: 2016-06-02 13:00
categories: Unity
---

在 Unity 项目里，脚本跟其它组件（component）一样，都是组件，可理解为“操作对象行为的组件”。本文意在整理 C# 脚本中目前用到的系统 API。可以看到，很多脚本都继承自 MonoBehaviour 类，这样我们就可以使用 GameObject, Transform, Start, Update 这些函数了。

<!-- more -->

---

#事件函数（Event Functions）

##更新顺序（Update Order）

我们先来看看在脚本[生命周期](https://docs.unity3d.com/Manual/ExecutionOrder.html)中，每个事件函数的执行顺序，见下图：

![](/images/unity/csharp-script/monobehaviour_flowchart.svg)

###Awake

> Awake is called when the script instance is being loaded.

> Awake 在脚本实例加载时被调用。

> Awake is used to initialize any variables or game state before the game starts. Awake is called only once during the lifetime of the script instance. Awake is called after all objects are initialized so you can safely speak to other objects or query them using eg. GameObject.FindWithTag. Each GameObject's Awake is called in a random order between objects. Because of this, you should use Awake to set up references between scripts, and use Start to pass any information back and forth. Awake is always called before any Start functions. This allows you to order initialization of scripts. Awake can not act as a coroutine.

> Awake 用于在游戏开始前，初始化变量和游戏状态。Awake 在脚本实例的生命周期里只被调用一次。Awake 在所有对象初始化后被调用，那么你可以安全地与其它对象交互，或使用 GameObject.FindWithTag 引用它们。每个 GameObject 的 Awake 被调用的顺序是随机的。因此，你应该使用 Awake 来在脚本间设置引用，并使用 Start 来回传递信息。Awake 通常在 Start 前被调用，这样你可以给脚本执行初始化。Awake 不能执行为 coroutine。

> Note for C# and Boo users: use Awake instead of the constructor for initialization, as the serialized state of the component is undefined at construction time. Awake is called once, just like the constructor.

> C# 或 Boo 用户请注意：使用 Awake 代替构造函数来初始化，因为 component 的序列化状态在构造时未定义。Awake 只被调用一次，跟构造函数一样。

###Start

> Start is called before the first frame update only if the script instance is enabled.

> Start 在第一帧 update 前，且脚本实例可用时被调用。

即是，在该脚本对应的 GameObject 实例化后，该脚本实例可用，Unity 在第一帧先执行 `Start`，再执行 `Update`。每个脚本里的 `Start` 只会被 Unity 调用一次。

###FixedUpdate

> FixedUpdate is often called more frequently than Update. It can be called multiple times per frame, if the frame rate is low and it may not be called between frames at all if the frame rate is high. All physics calculations and updates occur immediately after FixedUpdate. When applying movement calculations inside FixedUpdate, you do not need to multiply your values by Time.deltaTime. This is because FixedUpdate is called on a reliable timer, independent of the frame rate.

> FixedUpdate 时常会比 Update 调用得更频繁。如果帧率低，在一帧里可被多次调用。如果帧率高，在一帧里也许不会被调用。所有物理计算和更新都在 FixedUpdate 后发生。当在 FixedUpdate 里应用运动计算，你不需要在结果上乘以 Time.deltaTime。这是因为 FixedUpdate 根据一个可靠的计时器来调用，与帧率无关。

###OnTriggerEnter

> OnTriggerEnter is called when the Collider other enters the trigger.

> 当 Collider other 进入该 trigger，Unity 调用 OnTriggerEnter(Collider other)。

> This message is sent to the trigger collider and the rigidbody (or the collider if there is no rigidbody) that touches the trigger. Notes: Trigger events are only sent if one of the colliders also has a rigidbody attached. Trigger events will be sent to disabled MonoBehaviours, to allow enabling Behaviours in response to collisions.

> 这消息被发送到 trigger collider 和接触到该 trigger 的 rigidbody（如果没有 rigidbody 就发送到 collider）。注意：trigger 事件只有在其中一个 collider 绑定了 rigidbody 才发送。

###Update

> Update is called once per frame. It is the main workhorse function for frame updates.

> Update 在每帧被调用一次。这是每帧更新时主要的工作场所。

###LateUpdate

> LateUpdate is called once per frame, after Update has finished. Any calculations that are performed in Update will have completed when LateUpdate begins. A common use for LateUpdate would be a following third-person camera. If you make your character move and turn inside Update, you can perform all camera movement and rotation calculations in LateUpdate. This will ensure that the character has moved completely before the camera tracks its position.

> LateUpdate 在每帧被调用一次，且在 Update 完成后调用。在 Update 里执行的所有计算，在 LateUpdate 开始前都已完成。LateUpdate 的一个常用用法是，实现第三人称跟随摄像机。如果你让角色在 Update 里移动或转身，你可以在 LateUpdate 执行所有摄像机移动和旋转的计算。这能保证，在摄像机追踪角色位置前，角色已移动完毕。

---

#脚本 API

##Destroy

* 在运行时销毁脚本所绑定的 GameObject，并销毁其所有 component 和 child GameObject。
* 在运行时销毁脚本所绑定的 GameObject 的某个 component。
* 调用后不是马上销毁 GameObject，而是标记，在本帧结束时，统一销毁所有已标记的 GameObject。

##Time.DeltaTime

每两个 `Update` 或 `FixedUpdate` 调用之间的时间。

DeltaTime 通常用来“使随时间移动的物体能平滑移动”。什么意思？我们知道，两次 `Update` 或 `FixedUpdate` 调用之间的时间不固定，如果每次 `Update` 或 `FixedUpdate` 物体的移动距离都一样，那么速度都不一样了（速度 = 距离/时间），我们就会看到物体移动得一快一慢。想要使物体在每次 `Update` 或 `FixedUpdate` 里速度一样，只要保持“距离/时间”比不变就可以了，所以我们把距离乘以一个跟时间正相关的系数即可，这个系数就是 DeltaTime。

##Input.GetAxis

* `Input.GetAxis("Horizontal")` 获取玩家输入的横坐标。
* `Input.GetAxis("Vertical")` 获取玩家输入的纵坐标。

##GetComponent

该脚本绑定了 GameObject，`GetComponent<Rigidbody>()` 会搜索并返回 GameObject 绑定的 rigidbody 的引用。

---

#在 Unity Inspector 设置值

* 脚本里的 public 变量，能在该脚本绑定的 GameObject 的 Inspector 中设置值，或把相关用到的其它 GameObject 拖动到引用槽中，让脚本获取 OtherGameObjce 的引用（即在 A 的脚本中获取 B 的引用）。
* 脚本里用到的 Fire1, Fire2, Horizontal, Vertical，对应 Unity Editor Menu - Edit - Project Settings - Input - Axes。

---

#参考资料

* [Unity Manual - Execution Order of Event Functions](http://docs.unity3d.com/Manual/ExecutionOrder.html)
