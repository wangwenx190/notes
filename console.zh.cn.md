# C/C++控制台程序

- 控制台如何输出不同颜色和底纹的字体：
  - Linux：在输出流中插入占位符`\e[***`

    | 占位符 | 作用 |
    | ----- | ---- |
    | \e[0m | 关闭所有属性 |
    | \e[1m | 高亮 |
    | \e[2m | 亮度减半 |
    | \e[3m | 斜体 |
    | \e[4m | 下划线 |
    | \e[5m | 闪烁 |
    | \e[6m | 快闪 |
    | \e[7m | 反显 |
    | \e[8m | 消隐 |
    | \e[9m | 删除线（中间一道横线） |
    | \e[30m ~ \e[37m | 设置前景色，依次为黑、红、绿、黄、蓝、紫、深绿和白色 |
    | \e[40m ~ \e[47m | 设置背景色，依次为黑、红、绿、黄、蓝、紫、深绿和白色 |
    | \e[90m ~ \e[106m | 设置前景色（与30~37相同，只不过更加明亮） |
    | \e[nA | 光标上移n行 |
    | \e[nB | 光标下移n行 |
    | \e[nC | 光标右移n行 |
    | \e[nD | 光标左移n行 |
    | \e[y;xH | 设置光标位置 |
    | \e[2J | 清屏 |
    | \e[K | 清除从光标到行尾的内容 |
    | \e[s | 保存光标位置 |
    | \e[u | 恢复光标位置 |
    | \e[?25l | 隐藏光标 |
    | \e[?25h | 显示光标 |

    在实际使用过程中，可以同时组合多种配置，使用分号分隔，比如`\e[4;42m`可以输出绿色背景并带有下划线的文字。也可以连续使用命令，例如用`\e[2J;0;0H`可以清屏并将光标移到控制台左上角开始输出。
  - Windows：Win32 API `SetConsoleTextAttribute`

    ```c
    #include <windows.h>

    void NewLine(void);
    void ScrollScreenBuffer(HANDLE, INT);

    HANDLE hStdout, hStdin;
    CONSOLE_SCREEN_BUFFER_INFO csbiInfo;

    int main(void)
    {
        LPSTR lpszPrompt1 = "Type a line and press Enter, or q to quit: ";
        LPSTR lpszPrompt2 = "Type any key, or q to quit: ";
        CHAR chBuffer[256];
        DWORD cRead, cWritten, fdwMode, fdwOldMode;
        WORD wOldColorAttrs;

        // Get handles to STDIN and STDOUT.

        hStdin = GetStdHandle(STD_INPUT_HANDLE);
        hStdout = GetStdHandle(STD_OUTPUT_HANDLE);
        if (hStdin == INVALID_HANDLE_VALUE || hStdout == INVALID_HANDLE_VALUE)
        {
            MessageBox(NULL, TEXT("GetStdHandle"), TEXT("Console Error"), MB_OK);
            return 1;
        }

        // Save the current text colors.

        if (! GetConsoleScreenBufferInfo(hStdout, &csbiInfo))
        {
            MessageBox(NULL, TEXT("GetConsoleScreenBufferInfo"), TEXT("Console Error"), MB_OK);
            return 1;
        }

        wOldColorAttrs = csbiInfo.wAttributes;

        // Set the text attributes to draw red text on black background.

        if (! SetConsoleTextAttribute(hStdout, FOREGROUND_RED | FOREGROUND_INTENSITY))
        {
            MessageBox(NULL, TEXT("SetConsoleTextAttribute"), TEXT("Console Error"), MB_OK);
            return 1;
        }

        // Write to STDOUT and read from STDIN by using the default
        // modes. Input is echoed automatically, and ReadFile
        // does not return until a carriage return is typed.
        //
        // The default input modes are line, processed, and echo.
        // The default output modes are processed and wrap at EOL.

        while (1)
        {
            if (! WriteFile(
                hStdout,               // output handle
                lpszPrompt1,           // prompt string
                lstrlenA(lpszPrompt1), // string length
                &cWritten,             // bytes written
                NULL) )                // not overlapped
            {
                MessageBox(NULL, TEXT("WriteFile"), TEXT("Console Error"), MB_OK);
                return 1;
            }

            if (! ReadFile(
                hStdin,    // input handle
                chBuffer,  // buffer to read into
                255,       // size of buffer
                &cRead,    // actual bytes read
                NULL) )    // not overlapped
            break;
            if (chBuffer[0] == 'q') break;
        }

        // Turn off the line input and echo input modes

        if (! GetConsoleMode(hStdin, &fdwOldMode))
        {
           MessageBox(NULL, TEXT("GetConsoleMode"), TEXT("Console Error"), MB_OK);
           return 1;
        }

        fdwMode = fdwOldMode & ~(ENABLE_LINE_INPUT | ENABLE_ECHO_INPUT);
        if (! SetConsoleMode(hStdin, fdwMode))
        {
           MessageBox(NULL, TEXT("SetConsoleMode"), TEXT("Console Error"), MB_OK);
           return 1;
        }

        // ReadFile returns when any input is available.
        // WriteFile is used to echo input.

        NewLine();

        while (1)
        {
            if (! WriteFile(
                hStdout,               // output handle
                lpszPrompt2,           // prompt string
                lstrlenA(lpszPrompt2), // string length
                &cWritten,             // bytes written
                NULL) )                // not overlapped
            {
                MessageBox(NULL, TEXT("WriteFile"), TEXT("Console Error"), MB_OK);
                return 1;
            }

            if (! ReadFile(hStdin, chBuffer, 1, &cRead, NULL))
                break;
            if (chBuffer[0] == '\r')
                NewLine();
            else if (! WriteFile(hStdout, chBuffer, cRead, &cWritten, NULL))
                break;
            else
                NewLine();
            if (chBuffer[0] == 'q')
                break;
        }

        // Restore the original console mode.

        SetConsoleMode(hStdin, fdwOldMode);

        // Restore the original text colors.

        SetConsoleTextAttribute(hStdout, wOldColorAttrs);

        return 0;
    }

    // The NewLine function handles carriage returns when the processed
    // input mode is disabled. It gets the current cursor position
    // and resets it to the first cell of the next row.

    void NewLine(void)
    {
        if (! GetConsoleScreenBufferInfo(hStdout, &csbiInfo))
        {
            MessageBox(NULL, TEXT("GetConsoleScreenBufferInfo"), TEXT("Console Error"), MB_OK);
            return;
        }

        csbiInfo.dwCursorPosition.X = 0;

        // If it is the last line in the screen buffer, scroll
        // the buffer up.

        if ((csbiInfo.dwSize.Y-1) == csbiInfo.dwCursorPosition.Y)
        {
            ScrollScreenBuffer(hStdout, 1);
        }

        // Otherwise, advance the cursor to the next line.

        else csbiInfo.dwCursorPosition.Y += 1;

        if (! SetConsoleCursorPosition(hStdout, csbiInfo.dwCursorPosition))
        {
            MessageBox(NULL, TEXT("SetConsoleCursorPosition"), TEXT("Console Error"), MB_OK);
            return;
        }
    }

    void ScrollScreenBuffer(HANDLE h, INT x)
    {
        SMALL_RECT srctScrollRect, srctClipRect;
        CHAR_INFO chiFill;
        COORD coordDest;

        srctScrollRect.Left = 0;
        srctScrollRect.Top = 1;
        srctScrollRect.Right = csbiInfo.dwSize.X - (SHORT)x;
        srctScrollRect.Bottom = csbiInfo.dwSize.Y - (SHORT)x;

        // The destination for the scroll rectangle is one row up.

        coordDest.X = 0;
        coordDest.Y = 0;

        // The clipping rectangle is the same as the scrolling rectangle.
        // The destination row is left unchanged.

        srctClipRect = srctScrollRect;

        // Set the fill character and attributes.

        chiFill.Attributes = FOREGROUND_RED|FOREGROUND_INTENSITY;
        chiFill.Char.AsciiChar = (char)' ';

        // Scroll up one line.

        ScrollConsoleScreenBuffer(
            h,               // screen buffer handle
            &srctScrollRect, // scrolling rectangle
            &srctClipRect,   // clipping rectangle
            coordDest,       // top left destination cell
            &chiFill);       // fill character and color
    }
    ```

    摘自：<https://docs.microsoft.com/en-us/windows/console/using-the-high-level-input-and-output-functions>
- 控制台如何覆盖同一行进行输出？实现方法是将光标移到行首再重新输出，这样新输出的内容就会覆盖掉原来行的内容。
  1. 光标移到行首：
     - Linux：输出`\r\e[K`
     - Windows：输出`\r`或使用Win32 API `SetConsoleCursorPosition`
  2. 输出退格键：输出`\b`。
     但请注意，输出`\b`仅仅是把光标移到了最后一个字符之前，原来的字符仍然存在，除非用新的字符替换，例如`printf("phrase\b\b\b\b.new")`的输出为`ph.new`。
