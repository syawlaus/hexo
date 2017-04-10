title: DirectX 11 编程：初始化 Windows 窗口
date: 2016-04-26 12:00
categories: DirectX 11
---

在开始使用 DirectX 编程前，需要先创建一个 Windows 窗口，供 DirectX 使用。在创建窗口前，为了熟悉 Windows 编程，先写一个 Hello world 程序看看：

<!-- more -->

```cpp
/**************************************************************************
* includes
*/
#include <windows.h>    // include the basic windows header file


/**************************************************************************
* main entry
*/
// the entry point for any Windows program
int WINAPI WinMain(HINSTANCE hInstance,
                   HINSTANCE hPrevInstance,
                   LPSTR lpCmdLine,
                   int nShowCmd) {
    // create a "Hello World" message box using MessageBox()
    MessageBox(NULL,
        L"Hello World!",
        L"Just another Hello World program!",
        MB_ICONEXCLAMATION | MB_OK);

    // return 0 to Windows
    return 0;
}
```

`WINAPI` 的作用是颠倒传入的参数的顺序。一般函数的参数从右到左传入，而带有 WINAPI 的函数，参数从左到右传入。

`WinMain` 是 Windows 程序的入口，相当于 Win32 console 程序的 `main`。4 个形参的具体说明详见《[DirectX 11 编程：常用 Windows API](http://syawlaus.github.io/blog/directx11/frequently-used-windows-api/#WinMain)》。

`MessageBox` 函数用于创建一个 MessageBox。

上面的代码运行会显示：

![](/images/directx11/init_window/hello_world.png)

接下来可以把 MessageBox 替换为 Windows 窗口了。

创建窗口需要三步：

1. 注册窗口
2. 创建窗口
3. 显示窗口

所以 `WinMain` 的基本结构是：

```cpp
int WINAPI WinMain(HINSTANCE hInstance,
                   HINSTANCE hPrevInstance,
                   LPSTR lpCmdLine,
                   int nShowCmd) {
    createAndShowWindow(hInstance, nShowCmd);
    return 0;
}

void createAndShowWindow() {
    registerWindow();
    createWindow();
    showWindow();
}
```

接下来分别定义 `registerWindow`、`createWindow` 和 `showWindow`。

---

#registerWindow

注册窗口类（window class）时，需要调用 Windows 的 `RegisterClassEx` 函数，详见《[DirectX 11 编程：常用 Windows API](http://syawlaus.github.io/blog/directx11/frequently-used-windows-api/#RegisterClassEx)》。

在调用 `RegisterClassEx` 前，先填充好 WNDCLASSEX 结构体的字段：

```cpp
// this struct holds information for the window class
// the 'EX' is there to indicate that this is the extended version of the struct WNDCLASS
WNDCLASSEX wc;

// init memory
ZeroMemory(&wc, sizeof(WNDCLASSEX));

// fill in the struct with the needed information
wc.cbSize = sizeof(WNDCLASSEX);
wc.style = CS_HREDRAW | CS_VREDRAW;         // What these two values do is tell Windows to redraw the window if it is moved vertically or horizontally.
wc.lpfnWndProc = WindowProc;                // "lpfnWndProc" tells the window class what function to use ("WindowProc()") when it gets a message from Windows
wc.hInstance = hInstance;                   // a handle to a copy of our application
wc.hCursor = LoadCursor(NULL, IDC_ARROW);   // stores the default mouse image for the window class
wc.hbrBackground = (HBRUSH)COLOR_WINDOW;    // contains the "brush" (is used to indicate the color of the background) that will be used to color the background of our window. 
wc.lpszClassName = L"WindowClass1";         // the name of the window class we are building. The 'L' that appears before the string, simply tells the compiler that this string should be made of 16-bit Unicode characters, rather than the usual 8-bit ANSI characters.
```

`WindowProc` 是我们自定义的函数，用于窗口处理 Windows 消息（如鼠标点击、拖动、缩放大小），代码如下：

```cpp
// the main message handler for the program
LRESULT CALLBACK WindowProc(HWND hWnd,
                            UINT message,
                            WPARAM wParam,
                            LPARAM lParam) {
    // sort through and find what code to run for the message given
    switch (message) {
    case WM_DESTROY: {  // this message is read when the window is closed
        // close the application entirely
        PostQuitMessage(0);
        return 0;
    } break;
    }

    // Handle any messages the switch statement didn't
    return DefWindowProc(hWnd, message, wParam, lParam);
}
```

然后就可以调用 `RegisterClassEx` 函数了：

    RegisterClassEx(&wc);

---

#createWindow

创建窗口，需要调用 Windows 的 `CreateWindowEx` 函数，详见《[DirectX 11 编程：常用 Windows API](http://syawlaus.github.io/blog/directx11/frequently-used-windows-api/#CreateWindowEx)》。

代码如下：

    RECT wr = { 0, 0, 500, 400 };  // left, top, right, bottom
    AdjustWindowRect(&wr, WS_OVERLAPPEDWINDOW, FALSE);          // adjust the size, client size is 500 * 400

    // the handle for the window, filled by a function
    HWND hWnd = CreateWindowEx(NULL,
                               L"WindowClass1",                 // name of the window class
                               L"Our First Windowed Program",   // title of the window
                               WS_OVERLAPPEDWINDOW,             // window style
                               300,                             // x-position of the window
                               300,                             // y-position of the window
                               wr.right - wr.left,              // width of the window
                               wr.bottom - wr.top,              // height of the window
                               NULL,                            // we have no parent window, NULL
                               NULL,                            // we aren't using menus, NULL
                               hInstance,                       // application handle
                               NULL);                           // used with multiple windows, NULL

`AdjustWindowRect` 函数根据参数给出的“窗口内的矩形大小”（即除去窗口边框的矩形，下图的 Client Size），计算窗口（包括边框）的实际大小（下图的 Window Size），详见《[DirectX 11 编程：常用 Windows API](http://syawlaus.github.io/blog/directx11/frequently-used-windows-api/#AdjustWindowRect)》。

![](/images/directx11/init_window/client_size_window_size.png)

---

#showWindow

`showWindow` 只需要调用

    ShowWindow(HWND, int)

即可，`ShowWindow(HWND, int)` 用法详见《[DirectX 11 编程：常用 Windows API](http://syawlaus.github.io/blog/directx11/frequently-used-windows-api/#ShowWindow)》。

把创建窗口的三步整合，得到以下代码：

```cpp
/**************************************************************************
* includes
*/
#include <windows.h>    // include the basic windows header file


/*************************************************************************
* function prototypes
*/
void createAndShowWindow(HINSTANCE hInstance, int nCmdShow);
void registerWindow(HINSTANCE hInstance);
HWND createWindow(HINSTANCE hInstance);
void showWindow(HWND hWnd, int nCmdShow);
LRESULT CALLBACK WindowProc(HWND hWnd, UINT message, WPARAM wParam, LPARAM lParam);


/**************************************************************************
* function definitions
*/
void createAndShowWindow(HINSTANCE hInstance, int nCmdShow) {
    registerWindow(hInstance);
    HWND hWnd = createWindow(hInstance);
    showWindow(hWnd, nCmdShow);
}

void registerWindow(HINSTANCE hInstance) {
    // this struct holds information for the window class
    // the 'EX' is there to indicate that this is the extended version of the struct WNDCLASS
    WNDCLASSEX wc;

    // clear out the window class for use
    // ZeroMemory() is a function that initializes an entire block of memory to NULL.
    // The address provided in the first parameter sets where the block is to start.
    // The second parameter indicates how long the block is.
    ZeroMemory(&wc, sizeof(WNDCLASSEX));

    // fill in the struct with the needed information
    wc.cbSize = sizeof(WNDCLASSEX);
    wc.style = CS_HREDRAW | CS_VREDRAW;         // What these two values do is tell Windows to redraw the window if it is moved vertically or horizontally.
    wc.lpfnWndProc = WindowProc;                // "lpfnWndProc" tells the window class what function to use ("WindowProc()") when it gets a message from Windows
    wc.hInstance = hInstance;                   // a handle to a copy of our application
    wc.hCursor = LoadCursor(NULL, IDC_ARROW);   // stores the default mouse image for the window class
    wc.hbrBackground = (HBRUSH)COLOR_WINDOW;    // contains the "brush" (is used to indicate the color of the background) that will be used to color the background of our window. 
    wc.lpszClassName = L"WindowClass1";         // the name of the window class we are building. The 'L' that appears before the string, simply tells the compiler that this string should be made of 16-bit Unicode characters, rather than the usual 8-bit ANSI characters.

    RegisterClassEx(&wc);
}

HWND createWindow(HINSTANCE hInstance) {
    RECT wr = { 0, 0, 500, 400 };                               // left, top, right, bottom
    AdjustWindowRect(&wr, WS_OVERLAPPEDWINDOW, FALSE);          // adjust the size, client size is 500 * 400

    // the handle for the window, filled by a function
    HWND hWnd = CreateWindowEx(NULL,
                               L"WindowClass1",                 // name of the window class
                               L"Our First Windowed Program",   // title of the window
                               WS_OVERLAPPEDWINDOW,             // window style
                               300,                             // x-position of the window
                               300,                             // y-position of the window
                               wr.right - wr.left,              // width of the window
                               wr.bottom - wr.top,              // height of the window
                               NULL,                            // we have no parent window, NULL
                               NULL,                            // we aren't using menus, NULL
                               hInstance,                       // application handle
                               NULL);                           // used with multiple windows, NULL
    return hWnd;
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
    createAndShowWindow(hInstance, nShowCmd);
    return 0;
}

// the main message handler for the program
LRESULT CALLBACK WindowProc(HWND hWnd,
                            UINT message,
                            WPARAM wParam,
                            LPARAM lParam) {
    // sort through and find what code to run for the message given
    switch (message) {
    case WM_DESTROY: {  // this message is read when the window is closed
        // close the application entirely
        PostQuitMessage(0);
        return 0;
    } break;
    }

    // Handle any messages the switch statement didn't
    return DefWindowProc(hWnd, message, wParam, lParam);
}
```

运行后显示：

![](/images/directx11/init_window/init_window.png)

---

#参考资料

* [Lesson 2: A Primer of Basic Windows](http://www.directxtutorial.com/Lesson.aspx?lessonid=11-1-2)
* [Lesson 3: Creating a Window](http://www.directxtutorial.com/Lesson.aspx?lessonid=11-1-3)
* [Lesson 4: Window Size and Client Size](http://www.directxtutorial.com/Lesson.aspx?lessonid=11-1-4)
* [Walkthrough: Creating Windows Desktop Applications (C++)](https://msdn.microsoft.com/en-us/library/bb384843.aspx)
