# Win32 笔记
- 控制台程序隐藏命令行窗口：`#pragma comment(linker, "/SUBSYSTEM:WINDOWS /ENTRY:\"mainCRTStartup\"")`。如果编译时提示入口点错误，就把`mainCRTStartup`更改为`wmainCRTStartup`，是`UNICODE`的问题
- 窗口程序显示命令行窗口：`#pragma comment(linker, "/SUBSYSTEM:CONSOLE /ENTRY:\"WinMainCRTStartup\"")`。如果编译时提示入口点错误，就把`WinMainCRTStartup`更改为`wWinMainCRTStartup`，是`UNICODE`的问题
- 如何做到完美的开机自启：注册一个 Windows 服务，使用 Windows 服务来启动你的程序。这样做不仅可以使你的程序比任何程序都早运行，还可以做到以管理员权限运行而不弹出 UAC 提示框。注册服务时，注意选择为可与图形界面交互，否则无法启动 GUI 程序。很多资料都说，Windows Vista 之后的系统不支持服务程序启动 GUI 程序，但经过我的亲自测试，直到 Windows 10 1809 都还支持。
- 通过嵌入 Manifest 文件实现完美的高 DPI 缩放：https://msdn.microsoft.com/en-us/C9488338-D863-45DF-B5CB-7ED9B869A5E2
- 自定义开始屏幕磁贴：https://docs.microsoft.com/en-us/previous-versions/windows/apps/dn449733(v=win.10)
- 如何使用API将文件（夹）移动到回收站而不是直接彻底删除：
  ```cpp
  // 示例代码，请注意修改
  BOOL MoveToRecycleBin(LPCTSTR pszPath, BOOL bDelete)
  {
    SHFILEOPSTRUCT shDelFile;
    memset(&shDelFile, 0, sizeof(SHFILEOPSTRUCT));
    shDelFile.fFlags |= FOF_SILENT; // don't report progress
    shDelFile.fFlags |= FOF_NOERRORUI; // don't report errors
    shDelFile.fFlags |= FOF_NOCONFIRMATION; // don't confirm delete
    // Set SHFILEOPSTRUCT params for delete operation
    shDelFile.wFunc = FO_DELETE; // REQUIRED: delete operation
    shDelFile.pFrom = pszPath; // REQUIRED: which file(s)
    shDelFile.pTo = NULL; // MUST be NULL
    if (bDelete)
    {
      shDelFile.fFlags &= ~FOF_ALLOWUNDO; // don't use Recycle Bin if delete is requested
    }
    else
    {
      shDelFile.fFlags |= FOF_ALLOWUNDO; // move to Recycle Bin
    }
    return SHFileOperation(&shDelFile);
  }
  ```
