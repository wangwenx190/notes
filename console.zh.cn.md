# C/C++控制台程序

- 控制台如何输出不同颜色和底纹的字体：在输出流中插入控制字符`\x1b`

  | 控制字符 | 作用 |
  | ----- | ---- |
  | \x1b[0m | 关闭所有属性 |
  | \x1b[1m | 粗体/高亮 |
  | \x1b[4m | 下划线 |
  | \x1b[24m | 取消下划线 |
  | \x1b[7m | 交换前景色和背景色 |
  | \x1b[27m | 取消交换前景色和背景色 |
  | \x1b[30m ~ \x1b[37m | 设置前景色，依次为黑、红、绿、黄、蓝、紫、青和白色 |
  | \x1b[40m ~ \x1b[47m | 设置背景色，依次为黑、红、绿、黄、蓝、紫、青和白色 |
  | \x1b[90m ~ \x1b[97m | 设置前景色（与30~37相同，只不过更加明亮） |
  | \x1b[100m ~ \x1b[107m | 设置背景色（与40~47相同，只不过更加明亮） |
  | \x1b[nA | 光标上移n行 |
  | \x1b[nB | 光标下移n行 |
  | \x1b[nC | 光标前进（右移）n列 |
  | \x1b[nD | 光标后退（左移）n列 |
  | \x1b[nE | 光标下移到视图的第n行的开头 |
  | \x1b[nF | 光标上移到视图的第n行的开头 |
  | \x1b[nS | 将文本向上滚动n行 |
  | \x1b[nT | 将文本向下滚动n行 |
  | \x1b[nJ | 清屏（n是干什么的？） |
  | \x1b[nK | 清空第n行（没有n的话就是当前行） |
  | \x1b]2;strBEL | 设置窗口标题为str |

  注意事项：
  - 在实际使用过程中，可以同时组合多种配置，使用分号分隔，比如`\x1b[4;42m`可以输出绿色背景并带有下划线的文字
  - 此占位符能跨平台使用，但Windows平台要进行额外设置

    ```cpp
    bool enableVTMode() {
        // Set output mode to handle virtual terminal sequences
        const HANDLE hOut = GetStdHandle(STD_OUTPUT_HANDLE);
        if (hOut == INVALID_HANDLE_VALUE) {
            return false;
        }
        DWORD dwMode = 0;
        if (!GetConsoleMode(hOut, &dwMode)) {
            return false;
        }
        dwMode |= ENABLE_VIRTUAL_TERMINAL_PROCESSING;
        if (!SetConsoleMode(hOut, dwMode)) {
            return false;
        }
        return true;
    }
    ```

    在输出占位符之前调用一次此函数即可。此为一次性函数，在程序运行后调用一次即可，不过程序重启后还需再次调用。
  - MSDN参考：<https://docs.microsoft.com/en-us/windows/console/console-virtual-terminal-sequences>

- 控制台如何覆盖同一行进行输出？实现方法是将光标移到行首再重新输出，这样新输出的内容就会覆盖掉原来行的内容。
  1. 光标移到行首：输出`\r`

     Windows平台还可以使用Win32 API `SetConsoleCursorPosition`
  2. 输出退格键：输出`\b`。

     但请注意，输出`\b`仅仅是把光标移到了最后一个字符之前，原来的字符仍然存在，除非用新的字符替换，例如`printf("phrase\b\b\b\b.new")`的输出为`ph.new`。
- Windows平台和控制台有关的API

  | API | 作用 |
  | --- | --- |
  | AllocConsole | 创建一个控制台窗口（主要针对GUI程序） |
  | AttachConsole | 连接一个控制台（一个程序最多只能同时连接一个控制台） |
  | FreeConsole | 将当前进程与其对应的控制台取消连接 |
  | GetConsoleCP | 获取当前控制台的输入代码页 |
  | GetConsoleCursorInfo | 获取当前控制台的光标大小和可见性 |
  | GetConsoleDisplayMode | 获取当前控制台的显示模式（窗口/全屏） |
  | GetConsoleFontSize | 获取当前控制台的字体大小 |
  | GetConsoleMode | 获取当前控制台的模式（插入模式/快速编辑模式/等等） |
  | GetConsoleOriginalTitle | 获取当前控制台的原始标题 |
  | GetConsoleOutputCP | 获取当前控制台的输出代码页 |
  | GetConsoleTitle | 获取当前控制台的标题 |
  | GetConsoleWindow | 获取当前控制台窗口的句柄 |
  | GetCurrentConsoleFont | 获取当前控制台的字体 |
  | GetLargestConsoleWindowSize | 获取当前控制台最大的窗口大小 |
  | GetStdHandle | 获取stdin/stdout/stderr的句柄 |
  | ReadConsole | 从当前控制台读取 |
  | WriteConsole | 在当前控制台输出 |

  注意事项：
  - 以上以`Get`开头的函数几乎都有其对应的`Set`开头的函数，具体作用我想不用多说了
  - 代码页请参考：<https://docs.microsoft.com/en-us/windows/win32/intl/code-page-identifiers>
  - MSDN参考：<https://docs.microsoft.com/en-us/windows/console/console-functions>
