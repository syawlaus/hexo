title: 《3D 游戏编程大师技巧》第 3 章学习笔记
date: 2016-07-01 01:40
categories: 计算机图形学
---

最近在学习《3D 游戏编程大师技巧》第 3 章，摘抄一些我认为重要的信息，并添加上自己的一些思考。

<!-- more -->

> 本章将建立一个基于软件接口的虚拟计算机系统，它将支持线性 8/16 位帧缓存（双缓存）／输入设备以及声音和音乐处理功能。有了这个接口，本书剩余部分便可以将重点放在 3D 数学、图形和游戏编程上。

> 只要能够有一个支持输入、声音和音乐的双缓存、线性寻址图形系统通信，谁会在乎它的工作原理呢？我们的兴趣在于 3D 图形编程，而不是低级设置。由于我们 99% 的工作都与光栅化、纹理映射、光照、隐藏面消除等相关，所以这种方法是可行的。

> 创建一台通用虚拟计算机，再在上面进行 3D 图形试验和编写游戏。

> 我的最低底线是，要移植 3D 内容，只要能够创建双缓存显示，一切都将正常工作。

> 请牢记，只要理解了我提供的 API，将不需要了解有关 Win32 和 DirectX 的任何知识，因为我们对 3D 图形所做的任何处理都是由这些 API、帧缓存和 C/C++ 代码组成的。

> 虚拟计算机的主旨在于帮助我们将精力集中在 3D 而不是（Win32 和 DirectX）设置细节上。

> 我过去常说：“只要能够绘制像素和读取键盘输入，就能够编写 DOOM。” 的确是这样，您只需能够访问帧缓存，其它一切都很简单。由于本书主要介绍软件 3D 编程和光栅化，因此所有算法都没有使用任何形式的 3D 加速。我们将绘制多边形，进行光照计算，对帧缓存中的每个像素进行其它处理。

![](/images/computer-graphics/tricks-of-the-3d-game-programming-gurus/chapter3-notes/figure-3.1.png)

> 注意：您可能会说，在可以使用硬件的情况下，为什么使用软件呢？原因有 3 个：首先，要成为优秀的图形编程人员，必须知道如何自动动手来完成各项任务；其次，了解工作原理有助于理解硬件加速；最后，谁来编写硬件代码呢？

> 我们将设计这样一个系统，即基于一个可见的主显示缓存和一个不可见的离屏辅显示缓存。这两个缓存都应该是可线性寻址的。

![](/images/computer-graphics/tricks-of-the-3d-game-programming-gurus/chapter3-notes/figure-3.2.png)

![](/images/computer-graphics/tricks-of-the-3d-game-programming-gurus/chapter3-notes/figure-3.3.png)

![](/images/computer-graphics/tricks-of-the-3d-game-programming-gurus/chapter3-notes/figure-3.4.png)

![](/images/computer-graphics/tricks-of-the-3d-game-programming-gurus/chapter3-notes/figure-3.5.png)

![](/images/computer-graphics/tricks-of-the-3d-game-programming-gurus/chapter3-notes/figure-3.6.png)

> 这是一个非常有用的练习。基本上，我们设计一个可移植的图形系统。彩这些思想，将能够设计出这样的游戏引擎，即通过实现少数几个函数的“内部细节”，就能够将它移植到其它平台。

> 我们将逐层建立游戏控制台，直到实现虚拟计算机。首先我们将从方程中消除 Windows。

> 第一版本的游戏控制台应执行下列任务：
>   1. 打开窗口。
>   2. 调用用户定义的初始化函数 Game_Init()。
>   3. 进入到主 Windows 事件循环，处理消息，然后返回。
>   4. 调用用户定义的主工作函数 Game_Main()，它执行一次游戏逻辑循环，然后返回。
>   5. 回到第 3 步，直到用户关闭应用程序。
>   6. 调用用户定义的关闭函数 Game_Shutdown()，它执行清除工作。

> 如果能够实现一个执行步骤 1～6 中功能的外壳程序，则完全不用考虑 Windows，从而将精力集中在函数 Game_Init()、Game_Main()、Game_Shutdown() 上。

![](/images/computer-graphics/tricks-of-the-3d-game-programming-gurus/chapter3-notes/figure-3.7.png)

我们在本书[第 2 章学习笔记](syawlaus.github.io/blog/computer-graphics/tricks-of-the-3d-game-programming-gurus/chapter2-notes/)代码的基础上，构造第一版本的游戏控制台。

代码如下：

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
// 用于以方式读取键盘输入
#define KEY_DOWN(vk_code) ((GetAsyncKeyState(vk_code) & 0x8000) ? 1 : 0)


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
// 游戏控制台
int Game_Init(void* param = NULL);
int Game_Shutdown(void* param = NULL);
int Game_Main(void* param = NULL);


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

int Game_Init(void* param) {
    // 所有游戏初始化工作都在这个函数中执行

    // 成功返回
    return 1;
}

int Game_Shutdown(void* param) {
    // 在这个函数中关闭游戏并释放为游戏分配的所有资源

    // 成功返回
    return 1;
}

int Game_Main(void* param) {
    // 这是游戏的核心，将不断被实时调用
    // 它类似于 C 语言的 main()，所有游戏调用都是在这里进行的

    // 游戏逻辑

    // 检查用户是否要退出
    if (KEY_DOWN(VK_ESCAPE)) {
        PostMessage(main_window_handle, WM_DESTROY, 0, 0);
    }

    return 1;
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

    // 执行游戏控制台特有的初始化
    Game_Init();

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

    // 关闭游戏并释放所有资源
    Game_Shutdown();

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

> （上面的代码）创建一个窗口，调用 Game_Init()，在每次事件循环中调用 Game_Main()。最后，用户关闭该窗口时，将调用 Game_Shutdown()。

> 您只需要将全部游戏功能加入到这 3 个函数中，仅此而已——我们已经将一个 Windows 应用程序抽象成 3 个函数调用，其它工作都已经完成。

> 现在需要 DirectX 接口来模拟虚拟计算机，并提供基本的双缓存图形系统以及声音和输入功能。

> 如图 3.9 所示，T3DLIB1 是一个相当简单的 2D 引擎。实质上，它是一个 2D、8/16 位颜色、双缓存的 DirectX 引擎，支持任何分辨率和裁剪。该引擎还支持窗口模式和全屏模式，并负责所有设置工作。因此，不管是在窗口模式下还是在全屏模式下，都可以将像素写入辅助缓存，程序逻辑负责将辅助缓存复制到主缓存的处理细节。

![](/images/computer-graphics/tricks-of-the-3d-game-programming-gurus/chapter3-notes/figure-3.9.png)

现在我们利用作者编写的 T3DLIB 创建第二版本的游戏控制台。首先我们需要：

* 把 t3dlib1.h、t3dlib2.h、t3dlib3.h 添加到工程的 \Header Files 目录
* 把 t3dlib1.lib、t3dlib2.lib、t3dlib3.lib 添加到工程的 \Source Files 目录

工程目录见下图：

![](/images/computer-graphics/tricks-of-the-3d-game-programming-gurus/chapter3-notes/t3dconsole-solution-explorer.png)

然后在 Project Properties -> Configuration Properties -> VC++ Directores -> Include Directores 和 Library Directores 添加 DirectX9 的对应路径，其中 \DirectX9 是从本书光盘资源的 \DirectX\dx9sdkcp.exe 直接解压而来。见下图：

![](/images/computer-graphics/tricks-of-the-3d-game-programming-gurus/chapter3-notes/t3dconsole-vcpp-directories.png)

然后在 Project Properties -> Configuration Properties -> Linker -> Input -> Additional Dependencies 添加：

* ddraw.lib
* dsound.lib
* dinput.lib
* dinput8.lib
* winmm.lib

![](/images/computer-graphics/tricks-of-the-3d-game-programming-gurus/chapter3-notes/t3dconsole-linker-input-additional-dependencies.png)

接下来就是第二版本控制台的完整代码：

```cpp
/**************************************************************************
* includes
*/
#include <windows.h>    // include the basic windows header file
#include <windowsx.h> 
#include <mmsystem.h>
#include <iostream> // include important C/C++ stuff
#include <conio.h>
#include <stdlib.h>
#include <malloc.h>
#include <memory.h>
#include <string.h>
#include <stdarg.h>
#include <stdio.h> 
#include <math.h>
#include <io.h>
#include <fcntl.h>

#include <ddraw.h>  // directX includes
#include <dsound.h>
#include <dmksctrl.h>
#include <dmusici.h>
#include <dmusicc.h>
#include <dmusicf.h>
#include <dinput.h>
#include "T3DLIB1.h"    // DirectDraw 模块
#include "T3DLIB2.h"    // DirectInput 模块
#include "T3DLIB3.h"    // DirectSound / DirectMusic 模块


/**************************************************************************
* consts
*/
#define INITGUID    // 确保所有的 COM　接口可用，也可以不这样做，而是包含 .lib 文件 dxguid.lib
// 用于 Windows 接口的常量
#define WINDOW_CLASS_NAME "WIN3DCLASS"  // Windows 类名称
#define WINDOW_TITLE "T3D Graphics Console Version 2.0"
#define WINDOW_WIDTH  640
#define WINDOW_HEIGHT 480
#define WINDOW_BPP    16    // 窗口的位深（8、16、24 等）
                            // 如果采用窗口模式，但不是全屏，位深必须与系统位深相同
                            // 另外，如果位深为 8 位，将创建一个调色板，并将其与应用程序关联起来
#define WINDOWED_APP  1     // 0 全屏模式，1 窗口模式


/*************************************************************************
* global variables
*/
HWND main_window_handle = NULL;     // 用于存储窗口句柄
HINSTANCE main_instance = NULL;     // 用于存储实例
char buffer[256];                   // 用于打印文本


/*************************************************************************
* function prototypes
*/
void createAndShowWindow(HINSTANCE hInstance, int nCmdShow);
void registerWindow(HINSTANCE hInstance);
void createWindow(HINSTANCE hInstance);
void adjustClientSizeToRequestedSize();
LRESULT CALLBACK WindowProc(HWND hWnd, UINT message, WPARAM wParam, LPARAM lParam);
// 游戏控制台
int Game_Init(void* params = NULL);
int Game_Shutdown(void* params = NULL);
int Game_Main(void* params = NULL);


/**************************************************************************
* function definitions
*/
void createAndShowWindow(HINSTANCE hInstance, int nCmdShow) {
    registerWindow(hInstance);
    createWindow(hInstance);    // 将窗口句柄和实例存储到全局变量中
    ShowWindow(main_window_handle, SW_SHOW);
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
                               WINDOW_CLASS_NAME,                              // name of the window class
                               WINDOW_TITLE,                                   // title of the window
                               (WINDOWED_APP ? (WS_OVERLAPPED | WS_SYSMENU | WS_CAPTION) : (WS_POPUP | WS_VISIBLE)),  // window style
                               0,                                              // x-position of the window
                               0,                                              // y-position of the window
                               WINDOW_WIDTH,                                   // width of the window
                               WINDOW_HEIGHT,                                  // height of the window
                               NULL,                                           // we have no parent window, NULL
                               NULL,                                           // we aren't using menus, NULL
                               hInstance,                                      // application handle
                               NULL);                                          // used with multiple windows, NULL

    // 将窗口句柄和实例存储到全局变量中
    main_window_handle = hWnd;
    main_instance = hInstance;

    // 调整窗口大小，使 client_size 为 width * height
    adjustClientSizeToRequestedSize();
}

void adjustClientSizeToRequestedSize() {
    if (WINDOWED_APP) {
        // 调整窗口的大小，使 client_size 的大小等于请求的大小
        // 如果应该程序为窗口模式时，将由边框和控件调整
        // 应用程序不是窗口模式时，无需调整
        RECT window_rect = { 0, 0, WINDOW_WIDTH - 1, WINDOW_HEIGHT - 1 };

        // 调用函数来调整 window_rect
        AdjustWindowRectEx(&window_rect,
                           GetWindowStyle(main_window_handle),
                           GetMenu(main_window_handle) != NULL,
                           GetWindowExStyle(main_window_handle));

        // 将 client_size 偏移量保存到全局变量中，因为 DDraw_Flip() 要使用它们
        window_client_x0 = -window_rect.left;
        window_client_y0 = -window_rect.top;

        // 调用 MoveWindow() 来移动窗口
        MoveWindow(main_window_handle,
                   0,
                   0,
                   window_rect.right - window_rect.left,   // 宽度
                   window_rect.bottom - window_rect.top,   // 高度
                   FALSE);
    }
}

int Game_Init(void* params) {
    // 所有游戏初始化工作都在这个函数中执行

    // 启动 DirectDraw
    DDraw_Init(WINDOW_WIDTH,
               WINDOW_HEIGHT,
               WINDOW_BPP,
               WINDOWED_APP);

    // 初始化 DirectInput
    DInput_Init();

    // 接管键盘
    DInput_Init_Keyboard();

    // 在这里加入接管其它 DirectInput 设备的函数调用...

    // 初始化 DirectSound 和 DirectMusic
    DSound_Init();
    DMusic_Init();

    // 隐藏鼠标
    ShowCursor(FALSE);

    // 随机数生成器
    srand(Start_Clock());

    // 在这里加入初始化代码...

    // 成功返回
    return 1;
}

int Game_Shutdown(void* params) {
    // 在这个函数中关闭游戏并释放为游戏分配的所有资源

    // 关闭一切

    // 在这里加入释放为游戏分配的资源的代码...

    // 关闭 DirectSound
    DSound_Stop_All_Sounds();
    DSound_Delete_All_Sounds();
    DSound_Shutdown();

    // 关闭 DirectMusic
    DMusic_Delete_All_MIDI();
    DMusic_Shutdown();

    // 释放所有输出设备
    DInput_Release_Keyboard();

    // 关闭 DirectInput
    DInput_Shutdown();

    // 最后关闭 DirectDraw
    DDraw_Shutdown();

    // 成功返回
    return 1;
}

int Game_Main(void* params) {
    // 这是游戏核心，将不断地被实时调用
    // 它类似于 C 语言中的 main()，所有游戏调用都是在这里进行的

    int index;  // 循环变量

    // 启动定时时钟
    Start_Clock();

    // 清空缓存
    DDraw_Fill_Surface(lpddsback, 0);

    // 读取键盘和其它设备输入
    DInput_Read_Keyboard();

    // 在这里加入游戏逻辑代码...

    // 交换缓存
    DDraw_Flip();

    // 同步到 30 帧/秒
    Wait_Clock(30);

    // 检查用户是否要退出
    if (KEY_DOWN(VK_ESCAPE) ||
        keyboard_state[DIK_ESCAPE]) {
        PostMessage(main_window_handle, WM_DESTROY, 0, 0);
    }

    return 1;
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

    // 执行游戏控制台特有的初始化
    Game_Init();

    // 进入主事件循环
    MSG msg;
    while (true) {

        // 检测消息队列中是否有消息，如果有，读取它
        if (PeekMessage(&msg, NULL, 0, 0, PM_REMOVE)) {

            // 检测是否是退出消息
            if (msg.message == WM_QUIT) {
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

    // 关闭游戏并释放所有资源
    Game_Shutdown();

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
            // 结束绘制
            EndPaint(hWnd, &paintStruct);
            // 成功返回
            return 0;
        } break;
        case WM_DESTROY: {      // this message is read when the window is closed
            // close the application entirely
            PostQuitMessage(0);
            return 0;
        } break;
        default:
            break;
    }

    // Handle any messages the switch statement didn't
    return DefWindowProc(hWnd, message, wParam, lParam);
}
```

运行程序，编译器报错（一）：

    c:\program files (x86)\windows kits\8.1\include\um\winnt.h(342): error C2146: syntax error : missing ';' before identifier 'PVOID64'

查阅[相关资料](http://blog.csdn.net/fuquanjun/article/details/9273529)，出现这个错误的原因是，PVOID64 是一个宏，在 64 位编译下起作用，包含在（我本机的是）c:\program files (x86)\windows kits\8.1\include\shared\basetsd.h 中。但因为我们上面在 VC++ Directores 的 Include Directores 里包含了 dx9 的 \Include 目录，里面也有一个 basetsd.h，但这个 basetsd.h 里没有 PVOID64 的定义，且编译时优先使用这个目录的 basetsd.h，所以找不到 PVOID64，故而报错。

解决办法有：

办法一：把 dx9 的 \Include 的优先级下移，使编译器优先使用 c:\program files (x86)\windows kits\8.1\include\shared\basetsd.h。

办法二：改写 winnt.h 代码，在

    typedef  void  *PVOID;  
    typedef  void  *POINTER_64  PVOID64; 

之前增加一行： 

    #define  POINTER_64  __ptr64 

不过最好不要轻易改写 winnt.h。

办法三：移除 dx9 \Include 目录里的 basetsd.h（我使用这个）。

---

继续运行程序，编译器报错（二）：

    error C2065: 'index' : undeclared identifier

位置出现在 t3dlib1.cpp 的 Load_Animation_BOB 函数，见下图：

![](/images/computer-graphics/tricks-of-the-3d-game-programming-gurus/chapter3-notes/index-error.png)

    // set the end of the list to a -1
    bob->animations[anim_index][index] = -1;

在这行代码上面的 for 循环，使用 index 作为下标进行遍历，在离开 for 循环后，index 应该是指向 bob->animations[anim_index] 的末尾元素，所以只要把 index 放在 for 外面即可：

    // load data into
    int index;
    for (index=0; index<num_frames; index++)
        bob->animations[anim_index][index] = sequence[index];

---

继续运行程序，编译器报错（三）：

    t3dlib2.obj : error LNK2019: unresolved external symbol _DirectInput8Create@20 referenced in function "int __cdecl DInput_Init(void)" (?DInput_Init@@YAHXZ)

只要在 Project Properties -> Configuration Properties -> Linker -> Input -> Additional Dependencies 里加上 dinput8.lib 即可。

---

继续运行程序，成功显示游戏控制台窗口：

![](/images/computer-graphics/tricks-of-the-3d-game-programming-gurus/chapter3-notes/t3dconsole-complete.png)

由于窗口显示使用 16 色，所以看起来跟常规的 Windows 窗口不太一样。

---

> 应确保选择窗口模式时，窗口的客户区域（client-size）为 window_width * window_height

> 本章在《Windows 游戏编程大师技巧》和本书之间搭建了一座桥梁。您应该知道，3D 图形学研究的不是 DirectX 和 Windows，而是数学、数学还是数学。为进行 3D 图形编程，只需要一个包含一些函数和两个帧缓存的平台。
