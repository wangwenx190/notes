# C/C++控制台程序

- 控制台如何输出不同颜色和底纹的字体：在输出流中插入占位符`\x1b[***`

  | 占位符 | 作用 |
  | ----- | ---- |
  | \x1b[0m | 关闭所有属性 |
  | \x1b[1m | 高亮 |
  | \x1b[2m | 亮度减半 |
  | \x1b[3m | 斜体 |
  | \x1b[4m | 下划线 |
  | \x1b[5m | 闪烁 |
  | \x1b[6m | 快闪 |
  | \x1b[7m | 反显 |
  | \x1b[8m | 消隐 |
  | \x1b[9m | 删除线（中间一道横线） |
  | \x1b[30m ~ \x1b[37m | 设置前景色，依次为黑、红、绿、黄、蓝、紫、青和白色 |
  | \x1b[40m ~ \x1b[47m | 设置背景色，依次为黑、红、绿、黄、蓝、紫、青和白色 |
  | \x1b[90m ~ \x1b[106m | 设置前景色（与30~37相同，只不过更加明亮） |
  | \x1b[nA | 光标上移n行 |
  | \x1b[nB | 光标下移n行 |
  | \x1b[nC | 光标右移n行 |
  | \x1b[nD | 光标左移n行 |
  | \x1b[y;xH | 设置光标位置 |
  | \x1b[2J | 清屏 |
  | \x1b[K | 清除从光标到行尾的内容 |
  | \x1b[s | 保存光标位置 |
  | \x1b[u | 恢复光标位置 |
  | \x1b[?25l | 隐藏光标 |
  | \x1b[?25h | 显示光标 |

  注意事项：
  - 在实际使用过程中，可以同时组合多种配置，使用分号分隔，比如`\x1b[4;42m`可以输出绿色背景并带有下划线的文字。也可以连续使用命令，例如用`\x1b[2J;0;0H`可以清屏并将光标移到控制台左上角开始输出。
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

- 控制台如何覆盖同一行进行输出？实现方法是将光标移到行首再重新输出，这样新输出的内容就会覆盖掉原来行的内容。
  1. 光标移到行首：
     - Linux：输出`\r\x1b[K`
     - Windows：输出`\r`或使用Win32 API `SetConsoleCursorPosition`
  2. 输出退格键：输出`\b`。
     但请注意，输出`\b`仅仅是把光标移到了最后一个字符之前，原来的字符仍然存在，除非用新的字符替换，例如`printf("phrase\b\b\b\b.new")`的输出为`ph.new`。
