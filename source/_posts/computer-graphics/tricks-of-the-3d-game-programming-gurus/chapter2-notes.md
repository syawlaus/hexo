title: 《3D 游戏编程大师技巧》第 2 章学习笔记
date: 2016-06-30 23:20
categories: 计算机图形学
---

最近在学习《3D 游戏编程大师技巧》第 2 章，摘抄一些我认为重要的信息，并添加上自己的一些思考。

<!-- more -->

> Windows 是一个多任务多线程操作系统，也是一个事件驱动型操作系统。与大多数 DOS 程序不同，大多数 Windows 程序都等待用户做一些事情（从而引发事件），然后 Windows 响应该事件并采取行动，如图 2.1 所示。图 2.1 显示了很多应用程序窗口，它们分别发送事件或消息给 Windows 进行处理。Windows 处理一些消息或事件，但大多数消息或事件都传回应用程序进行处理。

![](/images/computer-graphics/tricks-of-the-3d-game-programming-gurus/chapter2-notes/figure-2.1.png)

对于图 2.1，**我的疑问：怎么判断系统事件应该分发到哪个应用程序的本地事件队列？**

> 所有 Windows 程序都从执行一个名为 WinMain() 的函数开始。

> 对于 WinMain() 函数，必须使用 WINAPI，否则，启动代码将不能正确地将参数递归给它。

> 配的目标是编写在 Windows 平台上运行的 3D 游戏，所以无需知道很多有关 Windows 编程的知识。实际上，需要的只是一个框架 Windows 程序，它打开窗口，处理消息并调用主游戏循环，仅此而已。

![](/images/computer-graphics/tricks-of-the-3d-game-programming-gurus/chapter2-notes/figure-2.3.png)

> 事件处理程序是一个回调函数，Windows 在窗口中发生需要处理的事件时，在主事件循环中调用该函数。

> 事件处理程序是由您编写的，因此可处理任何想处理的事件；其它的事件可传递给 Windows 进行处理。当然，应用程序处理的事件和消息越多，其功能越强。

![](/images/computer-graphics/tricks-of-the-3d-game-programming-gurus/chapter2-notes/figure-2.4.png)

**我的疑问：如果多个应用程序都想处理同一个事件，怎么办？**

> WM_PAINT 消息非常重要。窗口需要重新绘制时发送该消息，这意味着大多数时候您需要执行重绘操作。对于 DirectX 游戏，这无关紧要，因为我们将以 30～60 帧/秒的速度重绘屏幕，但对于普通 Windows 程序而言，这就很重要了。然而，对于 DirectX 应用程序，我们不希望 Windows 认为屏幕没有被重新绘制，所以需要让 Windows 知道我们已经对 WM_PAINT 消息进行处理了。

![](/images/computer-graphics/tricks-of-the-3d-game-programming-gurus/chapter2-notes/figure-2.6.png)

接下来我们编写一个完整的 Windows 程序，创建一个窗口，并等待用户关闭它。代码跟《[DirectX 11 编程：初始化 Windows 窗口](http://syawlaus.github.io/blog/directx11/init-window/)》很基本一样。

```cpp
/**************************************************************************
* includes
*/
#include <windows.h>    // include the basic windows header file


/**************************************************************************
* consts
*/
#define INITGUID
// 用于 Windows 接口的常量
#define WINDOW_CLASS_NAME "WIN3DCLASS"  // Windows 类名称


/*************************************************************************
* global variables
*/
HWND main_window_handle = NULL;     // 用于存储窗口句柄


/*************************************************************************
* function prototypes
*/
void createAndShowWindow(HINSTANCE hInstance, int nCmdShow);
void registerWindow(HINSTANCE hInstance);
void createWindow(HINSTANCE hInstance);
void showWindow(HWND hWnd, int nCmdShow);
LRESULT CALLBACK WindowProc(HWND hWnd, UINT message, WPARAM wParam, LPARAM lParam);


/**************************************************************************
* function definitions
*/
void createAndShowWindow(HINSTANCE hInstance, int nCmdShow) {
    registerWindow(hInstance);
    createWindow(hInstance);    // 将窗口句柄和实例存储到全局变量中
    showWindow(main_window_handle, nCmdShow);
}

void registerWindow(HINSTANCE hInstance) {
    // this struct holds information for the window class
    // the 'EX' is there to indicate that this is the extended version of the struct WNDCLASS
    WNDCLASSEX winclass;

    // clear out the window class for use
    // ZeroMemory() is a function that initializes an entire block of memory to NULL.
    // The address provided in the first parameter sets where the block is to start.
    // The second parameter indicates how long the block is.
    ZeroMemory(&winclass, sizeof(WNDCLASSEX));

    // fill in the struct with the needed information
    winclass.cbSize = sizeof(WNDCLASSEX);
    winclass.style = CS_HREDRAW |                                   // What these two values do is tell Windows to redraw the window if it is moved vertically or horizontally.
                     CS_VREDRAW |
                     CS_OWNDC |
                     CS_DBLCLKS;
    winclass.lpfnWndProc = WindowProc;                              // "lpfnWndProc" tells the window class what function to use ("WindowProc()") when it gets a message from Windows
    winclass.cbClsExtra = 0;
    winclass.cbWndExtra = 0;
    winclass.hInstance = hInstance;                                 // a handle to a copy of our application
    winclass.hIcon = LoadIcon(NULL, IDI_APPLICATION);
    winclass.hCursor = LoadCursor(NULL, IDC_ARROW);                 // stores the default mouse image for the window class
    winclass.hbrBackground = (HBRUSH)GetStockObject(BLACK_BRUSH);   // contains the "brush" (is used to indicate the color of the background) that will be used to color the background of our window. 
    winclass.lpszMenuName = NULL;
    winclass.lpszClassName = WINDOW_CLASS_NAME;                     // the name of the window class we are building.

    RegisterClassEx(&winclass);
}

void createWindow(HINSTANCE hInstance) {
    // the handle for the window, filled by a function
    HWND hWnd = CreateWindowEx(NULL,
                               WINDOW_CLASS_NAME,       // name of the window class
                               "Your Basic Window",     // title of the window
                               WS_OVERLAPPEDWINDOW,     // window style
                               0,                       // x-position of the window
                               0,                       // y-position of the window
                               400,                     // width of the window
                               400,                     // height of the window
                               NULL,                    // we have no parent window, NULL
                               NULL,                    // we aren't using menus, NULL
                               hInstance,               // application handle
                               NULL);                   // used with multiple windows, NULL

    main_window_handle = hWnd;
}

void showWindow(HWND hWnd, int nCmdShow) {
    ShowWindow(hWnd, nCmdShow);
}


/**************************************************************************
* main entry
*/
// the entry point for any Windows program
int WINAPI WinMain(HINSTANCE hInstance,
                   HINSTANCE hPrevInstance,
                   LPSTR lpCmdLine,
                   int nShowCmd) {

    // 创建并显示 Windows 窗口
    createAndShowWindow(hInstance, nShowCmd);

    MSG msg;
    // 进入主事件循环
    while (GetMessage(&msg, NULL, 0, 0)) {
        // 转换加速键
        TranslateMessage(&msg);

        // 将消息发送给 Window proc
        DispatchMessage(&msg);
    }

    // 返回到 Windows 操作系统
    return (msg.wParam);
}

// the main message handler for the program
LRESULT CALLBACK WindowProc(HWND hWnd,
                            UINT message,
                            WPARAM wParam,
                            LPARAM lParam) {

    // 这是系统的主消d息处理函数
    PAINTSTRUCT paintStruct;    // 用于 WM_PAINT
    HDC hdc;                    // Handle of Device Context，设备场景句柄

    // sort through and find what code to run for the message given
    switch (message) {
    case WM_CREATE: {
        // 执行初始化的代码
        return 0;
    } break;
    case WM_PAINT: {
        // 开始绘制
        hdc = BeginPaint(hWnd, &paintStruct);
        // 完成绘制
        EndPaint(hWnd, &paintStruct);
        // 成功返回
        return 0;
    } break;
    case WM_DESTROY: {      // this message is read when the window is closed
        PostQuitMessage(0); // close the application entirely
        return 0;
    } break;
    default:
        break;
    }

    // Handle any messages the switch statement didn't
    return DefWindowProc(hWnd, message, wParam, lParam);
}
```

效果如下图：

![](/images/computer-graphics/tricks-of-the-3d-game-programming-gurus/chapter2-notes/basic-window.png)

因为 WinMain 里调用的 [GetMessage 函数会一直等待消息队列](http://syawlaus.github.io/blog/directx11/frequently-used-windows-api/)，直到有消息可获取，才分发该消息。

> 在 GetMessage 等待时消息时，主事件循环实际上被阻断。我们必须想办法解决这个问题，因为我们需要连续地执行游戏处理，并在 Windows 发生时及时处理它。

我们使用 PeekMessage 代替 GetMessage，PeekMessage 检测消息队列中是否有消息，有则处理消息，没有则继续处理其它游戏逻辑并重复循环。

更新后的 WinMain 如下：

```cpp
// the entry point for any Windows program
int WINAPI WinMain(HINSTANCE hInstance,
                   HINSTANCE hPrevInstance,
                   LPSTR lpCmdLine,
                   int nShowCmd) {

    // 创建并显示 Windows 窗口
    createAndShowWindow(hInstance, nShowCmd);

    MSG msg;
    // 进入主事件循环
    while (true) {
        // 检测消息队列中是否有消息，如果有，读取它
        if (PeekMessage(&msg, NULL, 0, 0, PM_REMOVE)) {

            // 检测是否是退出消息
            if (msg.message = WM_QUIT) {
                break;
            }

            // 转换加速键
            TranslateMessage(&msg);

            // 将消息发送给 Window proc
            DispatchMessage(&msg);
        }

        // 主游戏处理逻辑
        Game_Main();
    }

    // 返回到 Windows 操作系统
    return (msg.wParam);
}
```

函数调用 Game_Main() 在生成一个动画帧或执行游戏逻辑后必须返回；否则，主窗口事件循环将无法处理消息。

> DirectX 是一种非常前卫的设计，它假定高级功能由硬件来实现。但是，如果硬件不支持某种功能，将发生什么事情呢？这就是双模式（HAL 和 HEL）设计的基础。

> 如果您请求 DirectX 做某项工作，并且由 HAL（Hardware Abstraction Layer）直接完成，硬件将完成该工作；否则，则通过 HEL（Hardware Emulation Layer），调用软件仿真完成该工作。

> COM 对象可以被改变而不需要重新编译原来的程序，这一特点是非常有用的。

![](/images/computer-graphics/tricks-of-the-3d-game-programming-gurus/chapter2-notes/figure-2.10.png)

> COM 规定规定您创建的所有接口都必须从一个名为 IUnknown 的特殊基类接口派生得到。

> 介绍这些内容（略）旨在说明一个原则，无论何时使用 DirectX 接口，务必使用最新版本的接口。

**我的疑问：DirectX 跟 COM 有什么关系？**
