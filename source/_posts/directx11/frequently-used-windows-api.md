title: DirectX 11 编程：常用 Windows API
date: 2016-04-28 21:00
categories: DirectX 11
---

在 DirectX 编程中，我们会经常使用到 Windows API，本文用于整理用到的 Windows API。

<!-- more -->

# 函数

## WinMain

作用：用户提供的 Windows 图形程序入口。

语法：

    int CALLBACK WinMain(
        _In_ HINSTANCE hInstance,
        _In_ HINSTANCE hPrevInstance,
        _In_ LPSTR     lpCmdLine,
        _In_ int       nCmdShow
    );

参数：

    HINSTANCE hInstance         应用程序当前实例的 handle。
    HINSTANCE hPrevInstance     应用程序上一个实例的 handle。
    LPSTR     lpCmdLine         应用程序的命令行，除了程序名外。
    int       nCmdShow          控制窗口如何显示。

[MSDN 页面][1]

## ZeroMemory

作用：用 0 填充一段内存。

语法：

    void ZeroMemory(
        PVOID destination,
        SIZE_T length
    );

参数：

    PVOID destination   指向（需要填充的）内存的起始地址的指针。
    SIZE_T length       （需要填充的）内存的字节数。

[MSDN 页面][2]。

---

## RegisterClassEx

作用：注册一个窗口类（window class），以便在之后的 `CreateWindow` 或 `CreateWindowEx` 函数中使用。

语法：

    ATOM WINAPI RegisterClassEx(
        _In_ const WNDCLASSEX* lpwcx
    );

参数：

    const WNDCLASSEX* lpwcx     指向 WNDCLASSEX 结构体的指针，在传入该结构体参数时，必须填充好相应的字段值。

[MSDN 页面][3]。

## CreateWindowEx

作用：使用扩展的窗口风格，创建一个重叠的、弹出的、或子窗口。跟 `CreateWindow` 函数类似。

语法：

    HWND WINAPI CreateWindowEx(
      _In_     DWORD     dwExStyle,
      _In_opt_ LPCTSTR   lpClassName,
      _In_opt_ LPCTSTR   lpWindowName,
      _In_     DWORD     dwStyle,
      _In_     int       x,
      _In_     int       y,
      _In_     int       nWidth,
      _In_     int       nHeight,
      _In_opt_ HWND      hWndParent,
      _In_opt_ HMENU     hMenu,
      _In_opt_ HINSTANCE hInstance,
      _In_opt_ LPVOID    lpParam
    );

## AdjustWindowRect

作用：根据参数给出的“窗口内的矩形大小”（即除去窗口边框的矩形），计算窗口（包含边框）的实际大小。

语法：

    BOOL WINAPI AdjustWindowRect(
        _Inout_ LPRECT lpRect,
        _In_    DWORD  dwStyle,
        _In_    BOOL   bMenu
    );

## ShowWindow

作用：设置窗口的显示状态。

语法：

    BOOL WINAPI ShowWindow(
        _In_ HWND hWnd,
        _In_ int  nCmdShow
    );

## GetMessage

作用：从调用线程的消息队列取出一个消息。直到一个消息上传到消息队列，本函数才分发该消息。与 GetMessage 不同，PeekMessage 函数在返回前不会等待一个消息上传（到消息队列）。

语法：

    BOOL WINAPI GetMessage(
      _Out_    LPMSG lpMsg,
      _In_opt_ HWND  hWnd,
      _In_     UINT  wMsgFilterMin,
      _In_     UINT  wMsgFilterMax
    );

---

# 结构体

## WNDCLASSEX 

作用：包含窗口类（window class）的信息，跟 `RegisterClassEx` 和 `GetClassInfoEx` 函数一起使用。

语法：

    typedef struct tagWNDCLASSEX {
        UINT      cbSize;
        UINT      style;
        WNDPROC   lpfnWndProc;
        int       cbClsExtra;
        int       cbWndExtra;
        HINSTANCE hInstance;
        HICON     hIcon;
        HCURSOR   hCursor;
        HBRUSH    hbrBackground;
        LPCTSTR   lpszMenuName;
        LPCTSTR   lpszClassName;
        HICON     hIconSm;
    } WNDCLASSEX, *PWNDCLASSEX;

[MSDN 页面][4]。


















[1]: https://msdn.microsoft.com/en-us/library/windows/desktop/ms633559(v=vs.85).aspx
[2]: https://msdn.microsoft.com/en-us/library/windows/desktop/aa366920(v=vs.85).aspx
[3]: https://msdn.microsoft.com/en-us/library/windows/desktop/ms633587(v=vs.85).aspx
[4]: https://msdn.microsoft.com/en-us/library/windows/desktop/ms633577(v=vs.85).aspx
