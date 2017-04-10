title: Android 学习之 - ubuntu 使用 Android 真机调试
date: 2015-05-14 09:00
categories: Android
---

我们在开发 Android app 时，调试是必不可少的，但原生模拟器的速度实在不敢恭维，最好是能连接真机调试。

<!-- more -->

连接真机后，我们使用 `$ adb devices` 查看已连接的设备，显示：

    syawlaus@ubuntu:~$ adb devices
    List of devices attached
    * daemon not running. starting it now on port 5037 *
    * daemon started successfully *

那么我们需要做一些设置。

首先设备打开开发者模式，并允许 USB 调试。

然后连接设备到电脑，在终端输入：

    $ lsusb

这个命令的作用是，列出系统 USB 总线信息，以及全部连接到电脑的 USB 设备的信息。

终端会显示：

![](/images/android/using-hardware-devices-in-ubuntu/lsusb.png)

可以看到，`lsusb` 列出了我使用的 Nexus 4 设备，`ID 18d1:4ee2` 表示：

* 18d1 -- `usb_device_descriptor.idVendor`，设备供应商 ID
* 4ee2 -- `usb_device_descriptor.idProduct`，具体的设备 ID

其它的信息，如 `Bus 001` 和 `Device 002` 等，详见 [lsusb命令 - Linux命令大全](http://man.linuxde.net/lsusb)。

接下来，在终端进入 `/etc/udev/rules.d/` 目录，新建 `51-android.rules` 文件并加上以下内容：

    # Google Nexus 4 18d1:4ee2
    SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", MODE="0666", GROUP="plugdev"

`/etc/udev/rules/51-android.rules` 这个文件的作用是，The rule specifies that a matching USB device should be created with permissions 0660, with group plugdev, and a symlink conveniently pointing to it.

保存并关闭文件，然后修改文件权限：

    $ chmod a+r /etc/udev/rules.d/51-android.rules

其中 `chmod` 的参数：

* `a` -- 对全部用户有效
* `r` -- 只读

最后重启 USB 和 adb 服务：

    $ service udev restart
    $ adb kill-server

再输入：

    $ adb devices

如果列出设备，则说明连接成功。那么我们在 Android Studio 就可以使用真机调试程序了。:)

![](/images/android/using-hardware-devices-in-ubuntu/choose_device.png)

---

**参考资料**

* [Linux下搭建Android开发环境及Android真机调试](http://blog.csdn.net/whucyl/article/details/17057859)
* [lsusb命令 - Linux命令大全](http://man.linuxde.net/lsusb)
* [Using Hardware Devices](http://developer.android.com/tools/device.html)
* [Linux udev 的原理、功能、使用和维护](http://www.eygle.com/digest/2012/03/linux_udev_devfs.html)
* [使用 udev 高效、动态地管理 Linux 设备文件](http://www.ibm.com/developerworks/cn/linux/l-cn-udev/)
* [Android Debug Bridge](http://developer.android.com/tools/help/adb.html)

---

**问题**

1. `/etc/udev/rules/51-android.rules` 这个文件有什么用？
2. `SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", MODE="0666", GROUP="plugdev"` 这句是什么意思？为什么加上就可以了？
3. `adb kill-server`、`adb devices` 这两个命令是什么意思？
