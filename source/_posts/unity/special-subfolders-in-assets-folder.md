title: Unity 学习 - /Assets 里面的特殊子文件夹
date: 2016-08-15 19:50
categories: Unity
---

本文用于整理 Unity 工程中 /Assets 里面的特殊子文件夹，以及每个子文件夹的作用。

<!-- more -->

---

#/Editor

以 Editor 命名的文件夹允许其中的脚本访问 Unity Editor 的API。如果脚本中使用了在 UnityEditor 命名空间中的类或方法，该脚本必须被放在名为 Editor 的文件夹中。

在 /Editor 中的脚本将不会被打包到发布的游戏中，它们仅仅只是在 Editor 模式中使用。

在项目中可以有多个 /Editor。

**注意**：

* 如果 /Editor 在特殊文件夹下，/Editor 必须是特殊文件夹的直接子文件夹（即第一层目录中），否则 /Editor 将不起作用。
* 如果 /Editor 在普通文件夹下，/Editor 可以处于普通文件夹的任何层级。

---

#/Plugins

将脚本中需要访问的原生插件（native plugins）放入到此文件夹中，它们将自动的被打入到游戏发布包中，

**注意**：

* /Plugins 必须放在 /Assets 下，不能放到其它地方。

插件文件类型：

* 在 Windows 平台，插件为 .dll
* 在 Mac 平台，插件为 .bundle
* 在 Linux 平台，插件为 .so

与 /Standard Assets 一样，/Plugins 的脚本将会被提前编译，这样允许它们被其它在 /Plugins 以外的脚本调用。

##/Plugins/Android

在 /Plugins/Android 里面放入任何需要被 Android 工程包含的 .JAR 文件（Java 插件使用），或 .so 文件（Android NDK 插件使用）。更多可以参考 [Building Plugins for Android](http://docs.unity3d.com/Documentation/Manual/PluginsForAndroid.html)。

例如读取通讯录，微信分享等调用原生 Java 功能接口的 JAR 编译后放在 /Plugins/Android。

---

#/Resources

不同于在 Unity Editor 中直接拖拽资源到 Inspector 脚本变量这种直接引用资源的方法，/Resources 是一个允许你在脚本里面直接通过路径和名字读取资源的文件夹。

**注意**：

* 正因为这个原因，所以在放置资源到 /Resources 时必须加以注意。因为即使在 /Resources 中的某些资源没有被用到但是都会被打包到最终的游戏包中。因为你通过脚本使用这些资源，而 Unity 没有办法检测到哪些资源有被用到哪些没有。

在 Unity 工程中可以有多个 /Resources，但是不建议这样做。

一旦你的游戏发布了，所有在 /Resources 的资源将会被打包进游戏包中，这也就意味着即使你的代码能够通过工程中的资源路径读取到里面的资源，但是在最后的发布包中 Resources 将不复存在。更多可以参考 [Loading Resources at Runtime](http://docs.unity3d.com/Documentation/Manual/LoadingResourcesatRuntime.html)。

**注意**：

* 当资源被 MonoBehaviour 脚本中的变量访问时，这些资源将会在 MonoBehaviour 脚本实例化的时候加载到内存中。如果引用的资源过大而你不想这样加载的话，你可以将体积过大的资源放到 /Resources，再通过 Resources.Load 来加载。当资源不再被使用时，你能通过 Object.Destroy 删除这个资源，最后再调用 Resources.UnloadUnusedAssets 释放它所占用的内存。

---

#/Standard Assets

在 /Standard Assets 中的脚本最先被编译。/Standard Assets 中的脚本（根据不同的语言）会被导出到

* Assembly-CSharp-firstpass.csproj
* Assembly-UnityScript-firstpass.csproj
* Assembly-Boo-firstpass.csproj

的 C# 项目中。将脚本放在 /Standard Assets 里，就可以用 C# 脚本来访问 JavaScript 脚本或其它语言的脚本。更多可以参考 [Special Folders and Script Compilation Order](http://docs.unity3d.com/Documentation/Manual/ScriptCompileOrderFolders.html)。

---

#/StreamingAssets

这里的文件会被拷贝到build文件夹中，不会修改（移动和网页版不同，他们会被嵌入到最终build文件中）。

任何在 /StreamingAssets 的资源都会原封不动地拷贝到发布版文件夹中（除了移动和网页发布包，/StreamingAssets 里面的资源将会打包到一个最终的发布包中），/StreamingAssets 的路径虽然在每个平台都不一样，但是可以统一通过 Application.streamingAssetsPath 获取到。更多可以参考 [Application.streamingAssetsPath](http://docs.unity3d.com/Documentation/ScriptReference/Application-streamingAssetsPath.html) 和 [Streaming Assets](http://docs.unity3d.com/Documentation/Manual/StreamingAssets.html)。
